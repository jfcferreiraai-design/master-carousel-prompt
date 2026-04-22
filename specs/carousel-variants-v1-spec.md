# Carousel Variants V1 Spec

## Status
Planning / build-prep

## Purpose
Define the first 3 copy-paste instruction systems for the dashboard.

This product is **not** an in-app carousel builder.
It is a **copy-paste instruction system**:
1. user sees a preview in the dashboard
2. user chooses a carousel style
3. user copies that style's full project instructions
4. user pastes them into a Claude Project
5. user starts a new chat
6. Claude asks intake questions
7. Claude generates a fully self-contained HTML carousel and export-ready slide system

The dashboard is the **delivery and selection layer**.
Claude is the **runtime**.
Each variation must therefore be a **self-contained instruction architecture**, not just a visual mood.

---

## Source Pattern We Are Building From
The baseline system already works because it combines:
- brand intake questions
- palette derivation from one primary color
- typography setup
- fixed 4:5 carousel architecture
- reusable components
- Instagram preview wrapper
- Playwright export rules

We are keeping that system logic and evolving the **visual language**, **slide sequencing**, and **variant-specific use cases**.

---

## V1 Design Principles
All variants must follow these rules:

1. **Scroll-stopping first slide**
   - Every variant must open with a clear hook.
   - No weak introduction slides.

2. **HTML-first output**
   - The AI must generate a fully self-contained HTML carousel.
   - The HTML must remain export-safe.

3. **Fixed 4:5 output**
   - Preview and export system stay aligned.
   - No variant may require a different base geometry.

4. **Variant-specific visual identity**
   - Each variant must feel visually different enough that a user can choose between them from preview alone.
   - They must not feel like the same template with minor color changes.

5. **One strong purpose per variant**
   - Each variant should serve a distinct use case.
   - Avoid overlap unless the visual behavior is meaningfully different.

6. **Prompt controls content, template controls layout**
   - The instruction system should gather inputs and generate structured slide content.
   - The layout logic should remain stable and deliberate.

7. **Optional user images must be handled gracefully**
   - Variants should support image-light and image-rich workflows.
   - Cover-image behavior must be explicit where relevant.

8. **No generic AI aesthetics**
   - Avoid default SaaS visuals, weak spacing, generic fonts, or overused purple-gradient-on-white patterns.

---

## Shared System Contract
These behaviors apply to all 3 variants unless explicitly overridden.

### Shared intake fields
Each instruction system should collect or infer:
- brand name
- Instagram handle
- primary brand color
- optional logo or initial
- font preference or font mode
- tone
- carousel topic
- carousel goal
- CTA goal
- optional website URL
- optional cover image
- optional supporting images

### Shared technical output rules
- aspect ratio: 4:5
- preview-safe layout
- export-safe HTML
- image-safe zones must be respected
- progress logic must remain clear and intentional
- final slide must clearly signal completion
- copy must fit predictable max lengths

### Shared content constraints
- hook should be concise and punchy
- body copy must be scannable
- slides should not become essay blocks
- each slide must have one dominant idea
- the best variants feel intentionally designed, not text-stuffed

---

## Variant 1 — Overlay Hook

### Working role
Informative / trend / opinion / news-style carousel with a highly visual cover.

### Best use cases
- trend commentary
- industry insight
- opinion-based content
- informational breakdowns
- mini-news / mini-report style posts
- founder or expert takes

### Core promise
A visually arresting, image-led carousel where the first slide feels like a premium editorial/news cover and the following slides turn the topic into fast, structured insight.

### Visual signature
- full-bleed or near-full-bleed cover image
- dark bottom fade or gradient overlay on the first slide
- oversized condensed or assertive headline positioned in the lower half
- bold, contemporary feel
- stronger contrast and sharper hierarchy than the baseline architecture
- text-led insight slides after the cover should feel clean and readable, not overloaded

### What must stay fixed
- cover slide always uses image-led overlay logic when a cover image is available
- first-slide hook must sit in a protected readable zone
- contrast must be strong enough for immediate feed readability
- this variant must feel the most scroll-stopping of the 3 on slide 1

### What can flex
- image crop behavior
- whether slides 2+ alternate between dark/light or stay within a tighter palette system
- whether supporting slides use data points, bullets, callout cards, or mini-sections

### Suggested slide sequence
1. Hook cover
2. Why this matters
3. Key point 1
4. Key point 2
5. Key point 3 or example
6. Takeaway / what to do with this
7. CTA / final thought

### Ideal content style
- concise
- sharp
- modern
- opinionated when needed
- high signal, low fluff

### Best image behavior
- strongest when user provides a cover image
- should still work without one by falling back to an abstract, tonal, graphic-first hero treatment

### CTA style
- lighter CTA than a sales carousel
- more like: save, share, follow, read, think, comment

### This variant should not become
- a heavy step-by-step educational system
- a promo/sales layout
- a dull article summary

---

## Variant 2 — Clean Insight

### Working role
Educational / problem-solution / transformation carousel with the clearest reading experience.

### Best use cases
- explaining a concept
- teaching a method
- breaking down mistakes
- showing a before/after logic without needing dramatic promo energy
- transforming a pain point into a structured solution

### Core promise
A clean, elegant, highly readable carousel designed to move the user from confusion or pain into clarity and action.

### Visual signature
- most editorial and readable of the 3
- cleaner spacing and stronger content hierarchy
- less visually loud than Overlay Hook
- cards, lists, numbered sections, or structured content blocks can appear here
- this is the most stable format for explanation-heavy content

### What must stay fixed
- must prioritize reading comfort and structure
- each slide should feel like one clear step or one clear idea
- visual design should support understanding, not dominate it
- this is the strongest variant for problem → explanation → solution → steps

### What can flex
- serif/sans pairings
- list patterns
- card styles
- amount of visual decoration
- optional supporting images or diagrams

### Suggested slide sequence
1. Hook
2. Problem / common mistake
3. Why it happens
4. What changes it
5. Step 1
6. Step 2 / example / outcome
7. CTA / takeaway

### Ideal content style
- clear
- structured
- confident
- practical
- easy to skim

### Best image behavior
- optional, not required
- can use screenshots, illustrations, simple diagrams, or no imagery at all
- must survive as a text-led carousel without looking empty

### CTA style
- practical CTA
- save this, use this, try this, follow for more, apply this process

### This variant should not become
- a news cover system
- a highly cinematic promo template
- a dense slide deck with too much body copy

---

## Variant 3 — Authority Promo

### Working role
Offer-driven / conversion-aware / authority-building carousel for coaches, consultants, service providers, and entrepreneurs.

### Best use cases
- service offer carousels
- lead magnet promotion
- authority + proof + CTA
- launch announcements
- premium positioning posts
- sales-support content

### Core promise
A premium, conversion-aware carousel that feels valuable and strategic rather than loud or spammy.

### Visual signature
- strongest CTA logic of the 3
- premium hierarchy inspired by cinematic ad logic but adapted to multi-slide storytelling
- more persuasive sequencing
- can use stronger contrast, richer backgrounds, glow, proof panels, offer callouts, and highlighted outcome statements

### What must stay fixed
- must feel premium, not cheap
- must move clearly toward a CTA
- should include perceived value and authority cues
- should never feel like a single-image ad split into 7 slides

### What can flex
- level of drama in typography
- proof slide treatment
- pricing or offer reveal behavior
- use of mockups, portraits, screenshots, or visual proof

### Suggested slide sequence
1. Hook / promise
2. Why this matters now
3. Pain or missed opportunity
4. What the offer / method gives
5. Proof / credibility / differentiator
6. What to do next
7. CTA / conversion slide

### Ideal content style
- confident
- persuasive
- premium
- concise
- outcome-led

### Best image behavior
- works well with portraits, product mockups, screenshots, testimonials, or branded visuals
- should support optional proof-heavy slides

### CTA style
- strongest CTA of the 3
- book, DM, join, download, apply, reserve, claim

### This variant should not become
- a spammy hard-sell carousel
- a square ad adapted poorly into slides
- a generic Canva-style promo post

---

## Comparison Matrix

| Variant | Best For | Visual Energy | Reading Comfort | CTA Strength | Image Dependence |
|---|---|---:|---:|---:|---:|
| Overlay Hook | trends, opinions, informative/news | High | Medium | Low-Medium | Medium-High |
| Clean Insight | education, problem-solution, transformation | Medium | High | Medium | Low-Medium |
| Authority Promo | offers, service authority, conversion support | High | Medium | High | Medium |

---

## Variant Boundaries
To avoid prompt blur, each variant must stay in its lane.

### Overlay Hook boundary
If the first slide does not feel image-led and attention-grabbing, it is drifting toward Clean Insight.

### Clean Insight boundary
If the slides begin prioritizing dramatic promo hierarchy over clarity, it is drifting toward Authority Promo.

### Authority Promo boundary
If the slides lose persuasive momentum and become mostly educational, it is drifting toward Clean Insight.

---

## What We Are Deliberately Not Solving Yet
- full universal compatibility across Claude, ChatGPT, and Codex
- a master meta-prompt that selects all variants automatically
- auto-scraping of Instagram profiles as a required input
- dynamic template switching inside a single instruction file
- dashboard build or frontend implementation
- local renderer implementation details

Those come later.

---

## Immediate Build Implication
The next step after this file is approved is:
1. define one shared payload schema
2. define one testing matrix
3. write the first instruction system for **Overlay Hook**
4. test it against the baseline architecture
5. iterate before writing the other two

---

## Decision
V1 starts with exactly these 3 instruction systems:
- Overlay Hook
- Clean Insight
- Authority Promo

More variants can be added later, but these 3 are the initial productized set.
