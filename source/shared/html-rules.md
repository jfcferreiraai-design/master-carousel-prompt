# HTML Rules

## Geometry

- Carousel ratio: 4:5 only
- Preview size: 420x525px
- Export size: 1080x1350px via `device_scale_factor`
- All layout sizing is calibrated to the 420px base width

## Structure Contract

```html
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
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
    <div class="ig-header"></div>
    <div class="carousel-viewport">
      <div class="carousel-track">
        <!-- slides -->
      </div>
    </div>
    <div class="ig-dots"></div>
    <div class="ig-actions"></div>
    <div class="ig-caption"></div>
  </div>
  <script>
    /* Drag/swipe interaction only */
    /* Dot update logic only */
  </script>
</body>
```

## Code Rules

- No inline styles on structural elements.
- No external JavaScript dependencies.
- No JavaScript beyond drag or swipe interaction and dot updates.
- Total HTML size must stay under 150KB when no images are embedded.

## Slide UI Rules

- Every slide must include a progress bar.
- Every slide must include a slide counter in `N/Total` format.
- Every slide except the last must include a swipe arrow.
- Slide content must use `padding-bottom: 52px` to clear the progress bar.

## Image Handling Rules

- Do not inline base64 image blobs in the main carousel HTML.
- Do not use giant data URIs in the main carousel HTML.
- Do not dump uploaded chat image data into `<style>` blocks or `src` attributes.

Allowed in the main HTML:
- CSS gradients
- CSS-based shapes or patterns
- Inline SVG icons or lightweight decorative SVG
- Stable external image URLs when appropriate

If the user provides an image:
- Use a lightweight asset reference only if the runtime can support it safely.
- If that would require dumping asset data into the HTML, use a strong CSS or SVG fallback instead.
- Keep asset embedding, if needed, as a separate export-prep step.
