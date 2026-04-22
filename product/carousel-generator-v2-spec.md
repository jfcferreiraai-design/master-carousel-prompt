# Carousel Generator V2 — System Spec

## Status
Active — applies to all test runs from Test 002 onward

## Context
This spec was written after Test 001 (Overlay Hook, PT-PT). It formalizes the four operating modes of the carousel generator and introduces strict behavioral rules that address the failures identified in test-001-results.md.

The V1 instruction system (`prompts/overlay-hook.md`) is preserved as the V1 baseline. V2 replaces it for all new test runs.

---

## Four Operating Modes

The carousel generator must operate in exactly four discrete modes. Each mode has a defined trigger, a defined set of actions, and a defined output.

---

### Mode 1 — Create

**Trigger:** User requests a new carousel

**What happens:**
1. Collect all required and strongly recommended inputs before generating anything (see input list in prompts/carousel-master-instruction-v2.md)
2. Derive the full 6-token color palette from the single primary color input
3. Select typography based on tone and font preference
4. Generate structured slide content following the variant's slide sequence
5. Render the complete carousel HTML with all slides and required embedded UI elements

**Output:**
- A brief one-paragraph summary of the chosen visual and content direction
- The complete carousel HTML rendered inside the Instagram preview frame (Preview Mode is automatically applied)
- No export script at this stage — export is triggered separately in Mode 3

**Rules:**
- Do not generate until all required inputs are collected
- Do not assume brand color, tone, or topic
- Do not include base64 image blobs in the HTML output — see Image Handling rules below
- Apply language-specific copy rules where relevant (see PT-PT rules below)
- HTML structure must follow the clean structure rules below

---

### Mode 2 — Preview

**Trigger:** Automatically applied after every Create or Copy Refinement run; also triggered when the user asks to see the carousel

**What happens:**
Render the carousel HTML inside a fully structured Instagram-style frame in chat.

**Required frame structure — non-negotiable:**
```
.ig-frame (420px wide, max-width enforced)
  .ig-header (avatar + handle + subtitle)
  .carousel-viewport (420×525px, overflow hidden)
    .carousel-track (all slides in a horizontal row)
      .slide × N (each 420×525px, position relative, overflow hidden)
  .ig-dots (one dot per slide, active dot highlighted)
  .ig-actions (heart, comment, share, bookmark SVG icons)
  .ig-caption (handle + short caption text + timestamp)
```

**Frame behavior rules:**
- `.ig-frame` width must be exactly 420px — no exceptions
- `.carousel-viewport` must be exactly 420×525px (4:5 ratio)
- Pointer-based drag interaction must be included so the user can swipe through slides in chat
- Dots must update as the user swipes
- The swipe arrow inside each slide is a separate embedded UI element — it is not part of the frame chrome
- No slide content or layout element may be wider than 420px
- The frame itself is never exported — it is preview-only chrome

**What preview mode must not do:**
- Truncate slides or render partial content
- Show slides as static images instead of live HTML
- Omit the header, dots, actions, or caption sections
- Allow the frame to exceed 420px in any responsive context

---

### Mode 3 — Export

**Trigger:** User explicitly requests slide export or download

**What happens:**
1. Produce the export-ready HTML file — identical to the carousel HTML but without any preview-only chrome
2. Produce the Playwright export script targeting each slide individually
3. Confirm the total slide count matches the script's `TOTAL_SLIDES` variable
4. Confirm the export output directory path

**Export architecture — non-negotiable:**
- Layout stays at 420px wide
- `device_scale_factor = 1080 / 420` scales the output to 1080×1350px
- **Never change the viewport to 1080px** — this breaks the layout
- Hide all Instagram frame chrome (`.ig-header`, `.ig-dots`, `.ig-actions`, `.ig-caption`) before capturing
- Strip border-radius and box-shadow from `.ig-frame` before capturing
- Capture each slide individually using track `translateX` — never full-page screenshot
- Wait for font loading before first capture (`wait_for_timeout(3000)`)
- Wait between slide transitions (`wait_for_timeout(400)`)
- Use `clip` parameter to capture exactly the viewport area

**Export script must include:**
- Explicit `TOTAL_SLIDES` constant at the top — must match actual slide count
- Explicit `INPUT_HTML` and `OUTPUT_DIR` paths
- Per-slide print confirmation: `Exported slide N/TOTAL`
- Browser close inside a try/finally block to prevent orphaned processes

**What export mode must not do:**
- Embed the IG frame chrome in the exported images
- Change the slide layout width during export
- Export all slides as a single screenshot
- Leave the browser process running after export

---

### Mode 4 — Copy Refinement

**Trigger:** User requests copy changes, copy improvements, or PT-PT refinement

**What happens:**
1. Review each slide's copy against the copy quality criteria for the language and variant
2. Identify lines that feel translated, unnatural, or off-tone
3. Rewrite only the copy — do not touch layout, colors, components, or HTML structure
4. Re-render the carousel in Preview Mode with the updated copy

**Rules:**
- Copy refinement must never trigger a full carousel rebuild
- HTML structure, color tokens, and component code must not change during a copy pass
- Each changed slide must be clearly identified with old and new copy side by side in the response
- The user must be able to approve or reject individual slide changes

---

## Strict Rules

### Square carousel ratio consistency

- 4:5 ratio is the only allowed format
- Preview: 420×525px
- Export: 1080×1350px via `device_scale_factor`
- All padding, font sizes, and component sizes are calibrated for the 420px base width
- Any change to this base width invalidates the entire system

---

### Instagram-style preview frame behavior

The Instagram preview frame is mandatory in every chat output that contains carousel content. It is never optional.

Required behavior:
- Frame appears automatically whenever the carousel HTML is displayed in chat
- Frame must include all five sections: header, viewport, dots, actions, caption
- Frame width is always 420px — controlled via `max-width: 420px` on `.ig-frame`
- Dots update on swipe
- Frame chrome (header, dots, actions, caption) must never appear in exported slides

Failure states that must be prevented:
- Carousel displayed as raw HTML without frame
- Frame without header, dots, or actions
- Frame wider than 420px in any context
- Frame chrome appearing in Playwright exports

---

### Export behavior per slide

Each slide is captured individually. The export process must:
1. Load the full carousel HTML in a headless Chromium instance
2. Set viewport to 420×525px with `device_scale_factor = 1080/420`
3. Wait for fonts to load
4. Hide all IG frame chrome elements
5. For each slide index 0 through N-1:
   - Set track `transform: translateX(-index × 420px)` with `transition: none`
   - Wait 400ms
   - Screenshot with `clip: {x:0, y:0, width:420, height:525}`
   - Save as `slide_{index+1}.png`
6. Close browser

The output must be N individual PNG files at 1080×1350px.

---

### UI controls visibility

Progress bar:
- Present on every slide, always
- Fills proportionally: `((index + 1) / total) × 100%`
- Adapts color to light or dark slide background
- Never obscured by content — all slide content must use `padding-bottom: 52px`

Swipe arrow:
- Present on every slide except the last
- Adapts to light or dark background
- Removed entirely (not hidden, not transparent) on the final slide

Slide counter:
- Format: `N/Total` (e.g. `1/7`)
- Positioned beside the progress bar
- Uses the same adaptive color system as the progress bar

---

### Image handling without giant inline base64 blobs

This rule is absolute and applies to all modes.

**Allowed in the main carousel HTML output:**
- CSS gradient backgrounds
- CSS-based shapes and patterns
- Inline SVG patterns and icons
- External image URLs where the image is publicly accessible and stable

**Not allowed in the main carousel HTML output:**
- Full base64-encoded image blobs inline in `<style>` or `src` attributes
- Large data URIs of any kind embedded directly in the HTML file body

**When the user provides an image:**
1. Reference the image by its local path in the HTML during the preview phase
2. Only embed as base64 in a separate export-prep step, in a dedicated asset-embedding script
3. Never inline the base64 directly into the main carousel HTML file

If no image is available, the system must fall back to a CSS or SVG-based background that looks strong — not a placeholder or blank area.

---

### Cleaner HTML structure

The carousel HTML must follow this structural contract:

```
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <!-- Google Fonts import -->
  <style>
    /* Color tokens */
    /* Typography base */
    /* Layout base */
    /* Slide component styles */
    /* Instagram frame styles */
  </style>
</head>
<body>
  <div class="ig-frame">
    <div class="ig-header">…</div>
    <div class="carousel-viewport">
      <div class="carousel-track">
        <!-- slides -->
      </div>
    </div>
    <div class="ig-dots">…</div>
    <div class="ig-actions">…</div>
    <div class="ig-caption">…</div>
  </div>
  <script>
    /* Drag/swipe interaction only */
    /* Dot update logic only */
  </script>
</body>
```

Rules:
- No inline styles on structural elements — use CSS classes
- Color tokens declared as CSS custom properties or named constants at the top of the `<style>` block
- No JavaScript beyond drag/swipe interaction and dot updates
- No external JS dependencies
- Comments allowed only for section separators, not line-by-line narration
- Total HTML file size must stay under 150KB when no images are embedded

---

### Natural PT-PT copy quality

These rules apply whenever PT-PT is the required language.

**The copy must read as natively written European Portuguese, not translated English.**

Specific rules:
- Avoid gerund constructions common in BR Portuguese (e.g. "estou fazendo" → use "estou a fazer")
- Use European vocabulary where it differs from Brazilian (e.g. "telemóvel" not "celular", "câmara" not "câmera", "autocarro" not "ônibus")
- Avoid overly literal English idiom mapping — rewrite idioms in their PT-PT equivalent form or find a naturally Portuguese expression
- Sentence rhythm in PT-PT is different from EN and from BR Portuguese — prefer tighter, more direct phrasing for marketing copy
- Do not use "você" in contexts where a PT-PT brand voice would omit the subject or use implied second person
- Headline copy must feel punchy and contemporary — not formal or academic
- Body copy must feel confident and clear — not hesitant or padded
- CTA copy must feel natural, not like a translated English call-to-action

Before finalizing any PT-PT carousel:
1. Read each slide's copy aloud mentally in the European Portuguese register
2. Flag any line that sounds like translated text
3. Rewrite flagged lines before presenting

---

### Premium branded marketing output quality

Every carousel output must meet this baseline quality standard:

Visual:
- The cover slide must feel like a premium editorial or news cover — not a Canva template, not a quote card
- Slides 2+ must feel clean, readable, and intentionally designed — not text-stuffed
- The overall visual system must feel coherent — one consistent brand identity across all slides

Copy:
- The hook must earn attention and create a reason to swipe
- Each slide's content must serve one clear purpose — no padding slides
- The CTA must feel like a natural conclusion, not a hard sell appended at the end

Production:
- The HTML must render identically in Chromium and Chrome
- Font loading must be accounted for (Google Fonts CDN with proper weight declarations)
- No layout breakage at 420px — test mentally before delivering
