# Design Fork Workflow

The baseline engine comes from the canonical swipe file:

- `archive/legacy/claude_instagram_carousel_generator_en.md`

Use that file's working product flow as the source of truth:

1. intake
2. preview
3. revise
4. export

New variants should be created as design forks of that baseline engine.

Only change design-layer sections such as:

- cover treatment
- typography direction
- spacing and density
- component style
- slide sequence emphasis
- CTA tone

Do not rewrite shared runtime behavior unless there is a real blocker.

Shared runtime behavior includes:

- intake flow
- preview flow
- edit and refinement flow
- export expectation
- Instagram preview frame contract
- 420px preview width
- Create vs Export separation
- PT-PT quality rules
- HTML and export safety rules, including no blob dumping in main preview HTML
