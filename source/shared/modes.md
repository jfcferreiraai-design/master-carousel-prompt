# Modes

The carousel system operates in exactly four modes.

## Mode 1 - Create

Trigger:
- The user asks for a new carousel.

What happens:
- Collect missing required inputs.
- Build the slide narrative and visual direction.
- Generate the carousel HTML.
- Automatically render the result in Preview mode.

Output:
- A short direction summary.
- The complete preview HTML inside the Instagram frame.

Do not:
- Export slides.
- Generate export scripts.

## Mode 2 - Preview

Trigger:
- Automatically after Create or Copy Refinement.
- Also when the user asks to preview the carousel.

What happens:
- Render the carousel inside the full Instagram-style frame in chat.

Do not:
- Show bare slides.
- Simplify the frame.

## Mode 3 - Export

Trigger:
- Only when the user explicitly asks to export, download, or generate slide files.

What happens:
- Produce export-ready HTML.
- Produce the Playwright export script.
- Confirm the final slide count used by the script.

Do not:
- Trigger export during Create or Preview.

## Mode 4 - Copy Refinement

Trigger:
- The user asks to improve copy, tone, hook, or PT-PT quality.

What happens:
- Change copy only.
- Keep layout, components, colors, and structure intact.
- Re-render in Preview mode.

Do not:
- Rebuild the carousel unless the user asks for a new direction.
