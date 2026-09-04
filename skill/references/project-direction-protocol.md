# Project direction protocol (Altra Labs M3 — positioning & sequencing)

How a premium project OPENS - before any prompt, any code, any asset.

## 1. Positioning before pixels
Answer three questions in writing:
- **Who is the brand?**
- **Who do we want to attract?**
- **What feeling must the site transmit?**
Transform the answers into a visual direction (aesthetic adjectives + the role of any
signature element, e.g. "premium, artistic, minimal; a 3D sculpture that participates in
the page's narrative"). The clearer the intention before building, the lower the chance of
template-generic output.

## 2. The direction file (permanent context)
Keep a text file IN the project with: core brand idea · visual direction · desired aesthetic
· homepage structure · references · behaviors/animations · every important decision.
Agents read it before working instead of being re-briefed every prompt.
(Our stack: this is `impeccable`'s PRODUCT.md/DESIGN.md - use those; don't invent a third
file. This skill's registers/tokens feed the "visual direction" section.)

## 3. Hero element & composition
- Signature 3D: licensed/existing asset as starting point is legitimate for SPEED - energy
  goes to composition, lighting, animation, integration (our no-Blender procedural path in
  webgl-r3f-doctrine.md remains the default when no asset fits; both are valid).
- The element has a narrative ROLE (changes as the user travels the page), never decoration.
- Composition from reference screenshots, stating exactly what to preserve: title position ·
  3D scale · amount of negative space · alignment · text/image relation.
- Refine ONE variable at a time: position → scale → rotation → shadow → light → typography →
  spacing. You direct intention; the agent solves implementation.

## 4. Page as chapters (sequencing)
A site is a SEQUENCE, not a stack: each section prepares the next. Workflow:
- Have the agent analyze the direction file + hero, then PLAN the full page hierarchy first
  (plan mode) - e.g. hero → selected work → strong positioning statement → process →
  services → closing.
- **The plan is a recommendation; you own the direction** - accept, reorder, cut.
- Each chapter gets its own identity (contrast, rhythm, clear transitions) inside ONE visual
  language - never "sections that look like they came from different places", never an
  endless run of same-shaped cards.
- Then refine section by section: narrative first (proof, positioning, process, closing),
  layout second, detail last.

Hero-first sequence: positioning → visual direction → main element → composition →
implement & refine detail by detail → its language governs everything after.

## 5. Working the visual elements (Altra M3 — visuals lesson)
- **Rules are defaults; the vision outranks them.** "White space matters, but I have a
  different idea for this composition" is a legitimate call - break a guideline
  deliberately when the compositional intention is clear and stated. (Doctrine protects
  against accident, not against direction.)
- **Handing assets to the agent**: give the images + placement ("statue center-left, concrete
  slab center-right") + the ILLUSION wanted ("make it read as embedded INTO the concrete"),
  plus a visual reference of the effect - and let the agent pick the technique (SVG mask,
  blend, displacement). Describe the result, not the implementation.
- **Visual bug reports = expected vs observed, tied to the trigger**: "fragments should
  appear one per step, transparent → fully visible as each wireframe completes with scroll;
  currently I can't see the state completing across steps. Analyze the problem and fix."
  Never just "it's broken". Then accept incrementally ("better - still needs X") - the
  constant loop is analyze, adjust, ELIMINATE until the composition matches the vision.
