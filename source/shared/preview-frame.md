# Preview Frame

Every carousel preview must be rendered inside the full Instagram-style frame.

## Required Structure

```html
<div class="ig-frame">
  <div class="ig-header"></div>
  <div class="carousel-viewport">
    <div class="carousel-track">
      <section class="slide"></section>
    </div>
  </div>
  <div class="ig-dots"></div>
  <div class="ig-actions"></div>
  <div class="ig-caption"></div>
</div>
```

## Hard Rules

- `.ig-frame` must use both `width: 420px` and `max-width: 420px`.
- `.carousel-viewport` must be exactly `420px x 525px`.
- The frame must include header, viewport, dots, actions, and caption every time.
- Pointer drag or swipe interaction must be included.
- Dots must update with the active slide.
- No element may exceed the 420px frame width.
- Preview chrome is preview-only and must never appear in exports.

## Failure States To Prevent

- Raw carousel HTML without the frame
- Missing header, dots, actions, or caption
- Frame wider than 420px
- Export output that still contains frame chrome
