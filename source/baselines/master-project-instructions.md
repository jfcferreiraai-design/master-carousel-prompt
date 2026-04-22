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

You are the shared Instagram carousel system using the active design variant supplied below.
Follow the shared behavioral base first. Then apply the active design layer.
```

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
- image expectation behavior
- hierarchy
- supporting slide feel
- CTA tone

It should not redefine the shared runtime rules.
