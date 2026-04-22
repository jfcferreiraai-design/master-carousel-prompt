# Payload Schema

## Status
Planning / build-prep

## Purpose
Define the shared structured content contract for the first 3 carousel instruction systems.

This schema is not the final runtime format enforced by code yet.
It is the **source-of-truth content contract** that all prompt systems should target.

The goal is simple:
- keep the 3 variants structurally compatible where possible
- separate **content generation** from **layout decisions**
- reduce prompt drift
- make local testing easier later

The 3 variants covered by this schema are:
- Overlay Hook
- Clean Insight
- Authority Promo

---

## Design Principle
The prompt should generate **structured carousel content**.
The template should decide **how that content is visually rendered**.

That means:
- the prompt should not freestyle layout logic on every run
- the prompt should fill a predictable schema
- the HTML/CSS system should remain stable and variant-aware

---

## Shared Schema Shape

```json
{
  "variant": "overlay-hook",
  "brand": {},
  "content": {},
  "assets": {},
  "rendering": {}
}
```

---

## 1. variant

### Purpose
Identifies which instruction system and visual template the output is for.

### Allowed values
- `overlay-hook`
- `clean-insight`
- `authority-promo`

---

## 2. brand

### Purpose
All reusable brand and tone inputs.

### Shape

```json
{
  "name": "",
  "handle": "",
  "primaryColor": "",
  "logoMode": "initial",
  "logoSvg": "",
  "fontMode": "editorial",
  "customHeadingFont": "",
  "customBodyFont": "",
  "tone": "",
  "websiteUrl": "",
  "tagline": ""
}
```

### Field notes
- `name`: brand or creator name
- `handle`: Instagram handle without requiring hard validation
- `primaryColor`: main accent color or best inferred color
- `logoMode`: `svg`, `initial`, or `none`
- `logoSvg`: optional raw SVG path/string only if available
- `fontMode`: style direction such as `editorial`, `modern`, `bold`, `minimal`, `technical`, `friendly`
- `customHeadingFont`: optional
- `customBodyFont`: optional
- `tone`: content tone, not visual style alone
- `websiteUrl`: optional brand reference source
- `tagline`: optional short brand line for first/last slide use

### Required in V1
Required unless clearly inferable:
- `name`
- `primaryColor`
- `tone`

Strongly recommended:
- `handle`
- `fontMode`

---

## 3. content

### Purpose
Holds the actual narrative and slide data.

### Shape

```json
{
  "topic": "",
  "goal": "",
  "ctaGoal": "",
  "carouselTitle": "",
  "hook": "",
  "subhook": "",
  "slides": []
}
```

### Field notes
- `topic`: what the carousel is about
- `goal`: what the carousel is trying to achieve overall
- `ctaGoal`: what the final action should encourage
- `carouselTitle`: optional internal or display title
- `hook`: first-slide primary line or headline
- `subhook`: optional supporting line
- `slides`: array of structured slide objects

### Required in V1
Required:
- `topic`
- `goal`
- `hook`
- `slides`

---

## 4. slides array

### Purpose
Defines each slide's role and structured content.

### Base shape

```json
[
  {
    "index": 1,
    "type": "hook",
    "tag": "",
    "headline": "",
    "subheadline": "",
    "body": "",
    "bullets": [],
    "stepNumber": "",
    "quote": "",
    "cta": "",
    "emphasis": [],
    "imageRole": "none",
    "imageRef": "",
    "notes": ""
  }
]
```

### Field notes
- `index`: slide number in sequence
- `type`: semantic slide role
- `tag`: optional small label above main headline
- `headline`: primary text
- `subheadline`: optional support line
- `body`: optional short paragraph or explanation
- `bullets`: optional list items
- `stepNumber`: only when relevant
- `quote`: optional quote, testimonial, or callout line
- `cta`: final action line if the slide needs it
- `emphasis`: array of words or phrases that deserve stronger styling
- `imageRole`: how imagery is used on that slide
- `imageRef`: optional pointer to a provided asset
- `notes`: non-user-facing implementation hints

---

## 5. allowed slide types

### Shared allowed types
- `hook`
- `context`
- `problem`
- `insight`
- `solution`
- `feature`
- `proof`
- `example`
- `steps`
- `details`
- `takeaway`
- `cta`

Not every variant should use all of them.

---

## 6. assets

### Purpose
Represents optional user-provided visuals.

### Shape

```json
{
  "coverImage": "",
  "supportImages": [],
  "screenshots": [],
  "mockups": [],
  "portraits": [],
  "imageStyleNotes": ""
}
```

### Field notes
- `coverImage`: best candidate for slide 1 when needed
- `supportImages`: general image pool
- `screenshots`: useful for proof/tutorial style content
- `mockups`: useful for promo or product content
- `portraits`: useful for expert/coach/service positioning
- `imageStyleNotes`: optional notes such as preferred crop or mood

### Required in V1
No asset field is globally required.
But `coverImage` is strongly recommended for `overlay-hook`.

---

## 7. rendering

### Purpose
Gives the variant-specific system enough direction without letting prompts redesign everything.

### Shape

```json
{
  "preferredSlideCount": 7,
  "allowImageHeavyCover": true,
  "allowTextHeavySlides": false,
  "ctaStrength": "medium",
  "visualTone": "premium",
  "density": "balanced"
}
```

### Field notes
- `preferredSlideCount`: default 5–7, 7 preferred in V1
- `allowImageHeavyCover`: important for Overlay Hook and Authority Promo
- `allowTextHeavySlides`: usually false; Clean Insight may tolerate more structured text
- `ctaStrength`: `low`, `medium`, `high`
- `visualTone`: short descriptor aligned to the variant
- `density`: `airy`, `balanced`, `dense`

---

## Variant-specific requirements

## A. Overlay Hook

### Intent
Visually striking informative / opinion / trend carousel.

### Minimum content requirements
- strong `hook`
- 1 image-capable cover slide
- 3–4 short insight slides
- one takeaway slide
- one CTA slide

### Additional rendering defaults
```json
{
  "preferredSlideCount": 7,
  "allowImageHeavyCover": true,
  "allowTextHeavySlides": false,
  "ctaStrength": "low",
  "visualTone": "bold-editorial",
  "density": "balanced"
}
```

### Best slide types
- `hook`
- `context`
- `insight`
- `example`
- `takeaway`
- `cta`

### Avoid
- too many bullets
- long body paragraphs
- overcomplicated feature lists

---

## B. Clean Insight

### Intent
Educational / explanatory / problem-solution carousel.

### Minimum content requirements
- strong `hook`
- problem or context framing
- explanation
- 1–2 structured steps, examples, or insight slides
- practical takeaway or CTA

### Additional rendering defaults
```json
{
  "preferredSlideCount": 7,
  "allowImageHeavyCover": false,
  "allowTextHeavySlides": true,
  "ctaStrength": "medium",
  "visualTone": "editorial-structured",
  "density": "balanced"
}
```

### Best slide types
- `hook`
- `problem`
- `insight`
- `solution`
- `steps`
- `example`
- `takeaway`
- `cta`

### Avoid
- cinematic ad-style layouts
- weak structure
- huge text blocks

---

## C. Authority Promo

### Intent
Offer-driven, authority-building, premium CTA carousel.

### Minimum content requirements
- promise-led hook
- why this matters / missed opportunity
- what the offer or method gives
- credibility / proof / differentiator slide
- direct CTA ending

### Additional rendering defaults
```json
{
  "preferredSlideCount": 7,
  "allowImageHeavyCover": true,
  "allowTextHeavySlides": false,
  "ctaStrength": "high",
  "visualTone": "premium-persuasive",
  "density": "balanced"
}
```

### Best slide types
- `hook`
- `problem`
- `solution`
- `feature`
- `proof`
- `details`
- `cta`

### Avoid
- looking like a spammy ad
- feeling like a square ad split into slides
- weak CTA clarity

---

## Copy constraints
These are not hard code constraints yet, but they should guide prompt output.

### Hook slide
- headline should be short enough to remain dominant
- subheadline optional, not mandatory
- one dominant message only

### Content slides
- one dominant idea per slide
- body copy should remain scannable
- bullets only when the variant supports them naturally

### CTA slide
- single main action
- no CTA overload
- should feel like completion, not just another content slide

---

## Why this matters
This schema protects us from the most common failure mode:
**prompts inventing both content and layout at the same time.**

Instead:
- prompts produce structured content
- variants interpret that content consistently
- testing becomes faster
- visual quality becomes more stable

---

## Immediate next use
After this file is approved:
1. define the testing matrix
2. write the first instruction system for `overlay-hook`
3. test whether the baseline Claude architecture can fill this schema cleanly
4. only then create the other two instruction systems
