# Test 001 - Overlay Hook Carousel (PT-PT)

## Status
Completed - results recorded in `tests/reports/overlay-hook/test-001-results.md`

## Objective
Validate the Overlay Hook instruction system's ability to generate a fully functional, export-ready Instagram carousel in European Portuguese (PT-PT), starting from the active standalone instruction file in `instructions/standalone/overlay-hook.md`.

The goal was to confirm whether the baseline system could:
- collect brand inputs and apply the Overlay Hook variant logic
- generate a well-structured carousel in HTML with an Instagram-style preview frame
- produce export-ready individual slide PNGs via the Playwright script
- deliver copy that reads as natural PT-PT, not translated English or BR Portuguese

---

## Content type
Informative / trend-led carousel - Overlay Hook variant

Topic class: industry commentary or insight-driven post directed at a PT-PT marketing audience

---

## Visual direction
- Premium editorial cover: image-led slide 1 with a dark fade overlay protecting headline readability
- Condensed assertive headline placed in the lower half of the cover slide
- Clean structured supporting slides with strong text hierarchy
- Light/dark slide alternation for visual rhythm
- Progress bar and swipe arrow present on every slide per spec
- No decorative clutter - premium restraint throughout

---

## Language requirement
**PT-PT - European Portuguese only**

Specific criteria:
- vocabulary and phrasing aligned with European Portuguese usage, not Brazilian
- professional register appropriate for Instagram content marketing in Portugal
- natural sentence rhythm and idiom - not a direct translation from English
- no Brazilianisms in vocabulary or grammar (e.g. avoid gerund constructions prevalent in BR Portuguese)
- no English-to-Portuguese idiom mapping that produces unnatural output

---

## Image and background requirement
No cover image was provided by the user for this test run.

The system was expected to fall back to:
- a CSS gradient or abstract tonal graphic hero treatment on slide 1
- no external image fetches at runtime
- no large embedded image blobs

Slide backgrounds should use only CSS gradients, CSS-based shapes, or inline SVG patterns.

---

## What the output was supposed to do

1. Display a fully swipeable carousel inside a correctly structured `.ig-frame` (420px wide) including: header with avatar and handle, 4:5 viewport, dot indicators, action icon row, and caption section

2. Show 6-7 slides following the Overlay Hook narrative arc:
- Slide 1: hook cover with overlay treatment
- Slides 2-6: structured insight slides (context, key points, takeaway)
- Slide 7: light CTA with no swipe arrow and full progress bar

3. Provide an export-ready HTML file and a Playwright script that produces individual slide PNGs at 1080x1350px using `device_scale_factor`, not viewport resize

4. Apply a brand-derived 6-token color palette from a single primary color input

5. Use PT-PT copy across all slides that reads as natively written, not translated
