# Testing Matrix

## Status
Planning / build-prep

## Purpose
Define the first controlled test set for the 3 carousel variants.

This testing matrix exists to stop us from evaluating outputs by vague taste alone.
We need repeatable fixtures that help answer:
- which variant works best for which use case
- where the instructions break
- where the layout breaks
- how much input is really necessary
- whether the same architecture survives across different business types

---

## Testing principles

1. **Same variants, different use cases**
   - The 3 variants must be tested against different business scenarios.

2. **Taste is not enough**
   - We are not just asking whether something looks cool.
   - We are testing functional fit, readability, output stability, and content-to-layout compatibility.

3. **One controlled prompt per run**
   - Do not improvise wildly during early tests.
   - Keep the instruction system stable while comparing outputs.

4. **Test with realistic inputs**
   - Use cases should resemble actual future customers.

5. **Start with static fixtures**
   - Before real users touch this, we test with fixed sample businesses and topics.

---

## What we are testing

Each run should help evaluate 4 things:

### A. Content quality
- is the hook strong?
- is the sequence logical?
- is the CTA appropriate?

### B. Variant fit
- does the chosen variant match the use case?
- or does the output feel like another variant would have worked better?

### C. Visual survivability
- does the content fit the template without crowding or awkward empty space?
- does the cover still look strong with the chosen inputs?

### D. Input burden
- how much user input was required?
- could a real buyer handle this without confusion?

---

## Evaluation scorecard
Each run should be scored 1–5 on:

| Dimension | Description |
|---|---|
| Hook strength | First slide stops the scroll |
| Variant fit | Output matches the intended style |
| Readability | Text hierarchy and content density feel right |
| Visual quality | Feels premium and intentional |
| Structural clarity | Slide sequence makes sense |
| CTA quality | Ending action feels appropriate |
| Input efficiency | Output quality relative to amount of user input |
| Reusability | Would this hold up across multiple topics? |

Optional note field:
- what broke?
- what felt generic?
- what should change in prompt vs template?

---

## V1 test cases

We start with 4 test fixtures.

---

## Test Case 1 — Consultant / Thought Leader

### Profile
A personal-brand consultant posting strategic insight content.

### Why this case matters
This is a strong fit for your target use of entrepreneur / coach / consultant content.
It tests whether the system can produce premium, authority-building carousels.

### Sample content topic
"Why most personal brands post consistently and still stay invisible"

### Inputs
- business type: consultant / strategist
- tone: premium, sharp, clear
- primary color: dark neutral + gold or muted accent
- optional portrait available
- no complex screenshots needed

### Best variants to test
- Overlay Hook
- Clean Insight
- Authority Promo

### What this case should expose
- whether Overlay Hook feels like a strong authority/news cover
- whether Clean Insight can explain an idea without becoming boring
- whether Authority Promo can stay premium without becoming salesy

---

## Test Case 2 — Beauty Business / Service Provider

### Profile
A salon, esthetician, or beauty service business promoting an insight or service-related theme.

### Why this case matters
This checks whether the system can later adapt back into beauty-business workflows.
It also tests image-heavy cover behavior and service-promo content.

### Sample content topic
"3 reasons your content is not turning into appointments"

### Inputs
- business type: beauty service
- tone: polished, warm, confidence-building
- primary color: feminine or luxury-leaning accent
- cover photo available
- optional treatment images available

### Best variants to test
- Overlay Hook
- Clean Insight
- Authority Promo

### What this case should expose
- whether image-heavy covers still feel readable
- whether the educational flow works for local-service offers
- whether promo logic can support bookings without feeling cheap

---

## Test Case 3 — Coach / Course Creator / Educator

### Profile
An expert who sells frameworks, education, and digital offers.

### Why this case matters
This is one of the most likely real buyers for a carousel instruction system.
It tests idea-driven content plus CTA-driven content.

### Sample content topic
"The 4 mistakes that make your offer sound generic"

### Inputs
- business type: coach / educator
- tone: clear, direct, premium
- primary color: strong, modern accent
- no cover image required
- optional mockup or screenshot can be used

### Best variants to test
- Clean Insight
- Authority Promo
- Overlay Hook as stretch case

### What this case should expose
- whether Clean Insight is the best educational engine
- whether Authority Promo can bridge education and selling elegantly
- whether Overlay Hook becomes too surface-level here

---

## Test Case 4 — Info / Trend / Commentary Post

### Profile
A creator or expert posting about a trend, shift, or opinion in their industry.

### Why this case matters
This is the most natural proving ground for Overlay Hook.
It tests whether the first-slide cover treatment truly earns attention.

### Sample content topic
"AI is making polished content cheaper — but not more persuasive"

### Inputs
- business type: creator / founder / commentator
- tone: sharp, modern, slightly opinionated
- primary color: bold neutral or strong accent
- cover image optional but recommended

### Best variants to test
- Overlay Hook
- Clean Insight

### What this case should expose
- whether Overlay Hook has a real reason to exist
- whether Clean Insight is too plain for trend-led content

---

## Variant-by-case priority map

| Test Case | Overlay Hook | Clean Insight | Authority Promo |
|---|---:|---:|---:|
| Consultant / Thought Leader | High | High | High |
| Beauty Business / Service Provider | High | High | High |
| Coach / Educator | Medium | High | High |
| Info / Trend / Commentary | High | Medium | Low |

---

## Test order
Do not test everything at once.

### Stage 1
Test only **Overlay Hook** against:
- Test Case 1
- Test Case 2
- Test Case 4

Reason:
Overlay Hook has the most visually distinctive behavior and the highest risk of breaking if the cover logic is weak.

### Stage 2
Test **Clean Insight** against:
- Test Case 1
- Test Case 2
- Test Case 3

Reason:
Clean Insight is likely the most broadly useful variant and needs to prove it can carry explanation-heavy content well.

### Stage 3
Test **Authority Promo** against:
- Test Case 1
- Test Case 2
- Test Case 3

Reason:
Authority Promo must prove it can persuade without looking cheap.

---

## Failure signals to watch for

### Overlay Hook failure signals
- cover feels like a generic quote card
- headline placement competes badly with the image
- fade overlay is too heavy or too weak
- follow-up slides lose momentum

### Clean Insight failure signals
- too much text
- slides feel bland or samey
- educational structure is correct but visually forgettable
- hierarchy is too timid

### Authority Promo failure signals
- feels like a square ad stretched into slides
- too many claims, not enough flow
- CTA is too aggressive relative to the content
- premium feel collapses into Canva energy

---

## What to record after every run
For every test run, save:
- variant name
- test case name
- exact user inputs
- generated output HTML
- exported slide previews
- scorecard results
- notes on what broke
- notes on whether the issue is:
  - prompt issue
  - content issue
  - template issue
  - visual style issue

---

## Decision rule
We move from planning to prompt iteration only when:
- one variant has completed at least 3 controlled runs
- we have identified its strongest and weakest case
- we know whether the main issues are prompt-side or template-side

---

## Immediate next use
After this file:
1. write the first instruction system for `overlay-hook`
2. run it against the stage 1 test cases
3. refine before building the next variant
