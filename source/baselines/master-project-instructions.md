# Master Project Instructions Base

## Role

This file models the inner Project Instructions block extracted from `archive/legacy/claude_instagram_carousel_generator_en.md`.

It is the shared behavioral runtime base for all design variants.
It does not include the outer setup wrapper.

## What Belongs Here

- The shared mode model
- Intake behavior
- Shared color and typography setup rules
- The mandatory Instagram preview frame contract
- The HTML and export contract
- PT-PT quality rules
- The no-blob-dumping rule for preview HTML

## Shared Base Opening

```md
# Instagram Carousel Generator - Project Instructions

You are an Instagram carousel design system. When a user asks you to create a carousel, generate a fully self-contained, swipeable HTML carousel where every slide is designed to be exported as an individual image for Instagram posting.
```

## Working Flow To Preserve

The working flow should stay close to the canonical swipe file:

1. Collect brand details and missing inputs
2. Derive the full color system
3. Set up typography
4. Define slide architecture and reusable components
5. Render the preview inside the Instagram frame
6. Revise requested slides or copy without rebuilding the whole direction unless needed
7. Export only after explicit approval or request, prioritizing final downloadable PNG or JPEG slide files first

Variants should keep this flow and user experience intact.

## Export Outcome Priority

When export is explicitly requested after preview approval:

- prioritize final downloadable PNG or JPEG slide files
- use export-ready HTML plus a Playwright export script only if direct file delivery is not possible
- exclude preview-only Instagram chrome from the exported result

## Shared Behavioral Base Sections

Author this layer from the shared sources below:

- `source/shared/system-contract.md`
- `source/shared/modes.md`
- `source/shared/intake.md`
- `source/shared/preview-frame.md`
- `source/shared/html-rules.md`
- `source/shared/export-rules.md`
- `source/shared/ptpt-quality.md`
- `source/shared/quality-filter.md`

## Design Variant Layer

After the shared behavioral base, append exactly one active design layer from `source/variants/`.

That design layer should control:
- cover treatment
- title placement and scale
- spacing and breathing room
- accent treatment
- image expectation behavior
- hierarchy
- supporting slide feel
- CTA tone

It should stay as close as possible to the shared working engine and should not redefine the shared runtime rules.
