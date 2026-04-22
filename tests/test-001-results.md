# Test 001 Results — Overlay Hook Carousel (PT-PT)

## Test reference
See `tests/test-001-overlay-hook-ptpt.md` for the full test brief.

## Overall outcome
Partial pass. The core carousel content was generated and recognizable as an Overlay Hook structure. However, four significant failures were identified that would block this output from being usable as a production-ready Instagram carousel.

---

## What worked well

- The Overlay Hook variant identity was present: the cover slide had editorial tone and lower-anchored headline placement
- The brand intake question flow worked correctly — inputs were collected before generation
- The 6-token color palette derivation from one primary color was applied consistently
- The progress bar component rendered correctly on most slides with correct fill percentages
- The swipe arrow was correctly hidden on the last slide
- The 7-slide sequence followed the intended Overlay Hook narrative arc (hook → context → insights → takeaway → CTA)
- The font scale and typographic hierarchy were coherent across slides
- The base content structure (headlines, body copy, tag labels) was logically sound

---

## What failed

### Problem 1 — No proper Instagram-style preview frame

The `.ig-frame` wrapper was either absent, structurally incomplete, or rendered without the required UI chrome.

The carousel did not display inside the expected Instagram-style frame: no post header with avatar and handle, no dot indicator row, no action icon strip, no caption area below the viewport.

Without this frame, the in-chat preview could not give the user a realistic read of what the post would look like in a feed. This is a blocking problem for usability evaluation.

### Problem 2 — Bad slide export and download completion

The Playwright export script or download mechanism did not complete cleanly.

Individual slides were not reliably produced as separate PNG files at 1080×1350px. Either the script failed partway through, slides were incomplete, or the exported files were not delivered to the user in a usable form.

This directly blocked the primary intended output: Instagram-ready individual slide images.

### Problem 3 — Huge base64 image injection in HTML

When a background image or visual asset was embedded in the HTML carousel output, the system used full inline base64 encoding directly inside the HTML file.

This produced excessively large HTML files — potentially several megabytes — making them slow to render, difficult to inspect in a browser or editor, and unreliable in headless Playwright rendering.

Base64 embedding of large assets inside the main carousel HTML is structurally wrong. It conflates the content artifact with the asset delivery mechanism.

### Problem 4 — PT-PT copy sounded too literally translated in parts

Several copy lines read as direct translations from English rather than naturally composed European Portuguese.

Problems included:
- idiom choices that are semantically correct but tonally unnatural in PT-PT
- sentence structures that mirror English syntax rather than flowing in natural Portuguese rhythm
- vocabulary choices common in BR Portuguese or in translated marketing content, not in native PT-PT writing

The copy was technically grammatically correct but would not pass as natively written content for a Portuguese marketing audience.

---

## What should be preserved

- The Overlay Hook slide structure and 7-slide narrative arc
- The brand intake question system (ask before generating, do not assume)
- The 6-token color palette derivation pipeline from a single primary color
- The progress bar and swipe arrow component behavior and adaptive light/dark logic
- The font scale and `.serif` / `.sans` class system
- The Playwright `device_scale_factor` export architecture (correct approach, broken execution)
- The 420px base layout width rule
- The 4:5 aspect ratio (420×525px at preview scale, 1080×1350px at export)

---

## What must change next

1. The Instagram preview frame must be enforced as a required, fully structured wrapper in every output — not optional, not a reduced version

2. The Playwright export must have a clean, verified execution path with explicit slide-by-slide confirmation

3. Image handling must use external URL references or CSS-only backgrounds by default — full base64 inline blobs must not appear in the main HTML output under any circumstances

4. PT-PT copy must be treated as a first-class concern with specific linguistic rules, not as a translation afterthought

5. HTML structure must be modular — styles, layout, and content should be clearly separated; image data must never be mixed into the carousel HTML body

6. These changes should be codified in `product/carousel-generator-v2-spec.md` and reflected in `prompts/carousel-master-instruction-v2.md`
