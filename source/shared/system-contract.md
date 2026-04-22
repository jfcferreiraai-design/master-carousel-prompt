# Shared System Contract

## Status

Active internal source for the Claude-first carousel instruction systems.

## Runtime Target

- The runtime is Claude Projects in the browser app or desktop app.
- The repo is the source of truth for authoring and refinement.
- Final deliverables are paste-ready Claude Project Instructions files.
- Shared source files are internal references, not end-user artifacts.

## Locked Constraints

- The full Instagram preview frame is mandatory.
- Preview stays fixed at 420px wide in a 4:5 ratio.
- Create and Export remain separate modes.
- Export stays explicit and controlled.
- No inline base64 blobs or giant data URIs in the main HTML.
- PT-PT must read as natively written, not translated.
- Slide 1 must be strong, especially for Overlay Hook.
- Uploaded chat images must not trigger blob dumping into the HTML.

## Active Architecture

- Shared reusable logic lives in `/source/shared/`.
- Variant-specific logic lives in `/source/variants/`.
- Shipping files live in `/instructions/standalone/`.
- Legacy prompt surfaces belong in `/archive/legacy/`.

## Non-Goals

- No local automation as the primary deliverable.
- No master end-user instruction file that tries to cover every variant.
- No speculative files for variants that are not being built yet.
