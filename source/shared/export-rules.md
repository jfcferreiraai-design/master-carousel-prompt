# Export Rules

Export is a separate mode. It must only run when the user explicitly asks for it.

## Export Contract

- Keep the layout at 420px wide.
- Use `device_scale_factor = 1080 / 420`.
- Never resize the viewport to 1080px.
- Hide preview-only Instagram chrome before capture.
- Capture one PNG per slide.

## Required Script Behavior

- `TOTAL_SLIDES` must match the actual carousel.
- `viewport={"width": 420, "height": 525}` must be used.
- Wait for fonts before the first capture.
- Remove frame border radius and shadow before capture.
- Move the track slide by slide using `translateX(-index * 420px)`.
- Capture with `clip={"x": 0, "y": 0, "width": 420, "height": 525}`.
- Close the browser cleanly.

## Export Must Not

- Run automatically after Create
- Include `.ig-header`, `.ig-dots`, `.ig-actions`, or `.ig-caption` in the PNGs
- Capture all slides in one image
- Change the base layout width
