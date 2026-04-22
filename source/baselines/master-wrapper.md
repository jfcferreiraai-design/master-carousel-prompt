# Master Wrapper

## Role

This file models the outer setup or delivery shell extracted from `archive/legacy/claude_instagram_carousel_generator_en.md`.

It is the reusable wrapper around a paste-ready Claude Project Instructions block.
It is not the behavioral runtime itself.

## What Belongs Here

- The setup or how-to instructions for creating a Claude Project
- The copy-and-paste handoff language
- The fenced code block that contains the actual Project Instructions
- Optional example prompts or delivery tips below the code block

## Reusable Wrapper Shape

````md
# Claude Instagram Carousel Generator - Project Instructions

## How To Set This Up

1. Go to Claude Projects.
2. Create or open the target project.
3. Open the Project Instructions field.
4. Copy the full instructions block below.
5. Paste it into Project Instructions.
6. Start a new project chat.

## Project Instructions - Copy Everything Below

> Paste these instructions into a Claude Project.

```md
[paste one standalone variant Project Instructions block here]
```

## Example Prompts To Try

- Add variant-relevant example prompts only if they help the delivery shell.

## Pro Tips

- Add lightweight usage tips only if they help the copy-paste workflow.
````

## Notes

- Keep this shell reusable across future variants.
- Do not let wrapper copy leak into the inner Project Instructions block.
