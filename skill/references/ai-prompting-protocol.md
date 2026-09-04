# AI prompting protocol (Altra Labs — structure lesson)

How to turn a build plan into instructions an AI executes well. This is also how THIS skill
should operate when generating: never start from a vague ask.

## The 5-part prompt
1. **Context** — what we're building and for whom ("premium landing for an architecture
   studio targeting high-end residential clients").
2. **Stack** — only when relevant (framework; GSAP/three only if already known to be needed).
3. **Structure** — the build plan verbatim: sections in order, each with enough context for a
   first composition ("fixed nav · full-screen hero · 3-col services grid · split about ·
   testimonial · final CTA"). You're asking for ARCHITECTURE, not "a site".
4. **Behavior** — how layout responds ("grid 3-col desktop → 1-col mobile; nav stays fixed;
   subtle hero parallax"). Motion detail comes LATER, separately.
5. **Constraints** — what must NOT happen: "no generic card grid, no placeholder copy, no
   unnecessary gradients, responsive required, only the libraries defined". Prompting is
   reducing the space where bad decisions can happen.

## Generation workflow
- First generation = **draft that solves altitude** (structure), never the final. Don't
  expect spacing/motion/detail perfection at once.
- **Refine one section at a time**, starting at the hero. When it sings, use it as the
  visual reference: "match this section's spacing, typography and refinement level in the
  next sections."
- Never "redo everything" — changing 10 things at once destroys your ability to see what
  improved.
- Precision of vocabulary = precision of output. Name what you see: grid, split layout,
  white space, hierarchy, parallax, stagger, scrub, responsive behavior — instead of
  "make it cooler".

## How this skill applies it to itself
When building from scratch: write the build plan (build-plan-protocol.md) → expand into the
5 parts → generate structure → verify at 390/1440 → refine hero → propagate its standard.
Motion is a separate pass (motion doctrine + teardown vocabulary).

---

# Motion prompting (Altra Labs — motion lesson, the pair of the above)

You don't need to master animation tech — you need to describe **what happens, when, and how
it should feel**. Never "add an animation" (leaves every decision open) and never implementation
words (opacity/translate/easing curves) in the first pass.

## The behavior vocabulary (one word each, prompt-ready)
- **Reveal** — how ONE element enters: "starts less visible, appears gradually, rises slightly."
- **Stagger** — how SIMILAR elements enter relative to each other: "one after another with
  small intervals" (order + feeling; no numbers in the first prompt).
- **Hover** — response to proximity; must communicate ONE thing (usually "this is interactive").
- **Micro-interaction** — response to a specific action (accordion opens, item selects);
  confirms the action registered; never flashy.
- **Parallax** — describe the RELATION, not the effect: "background image moves slightly
  slower than the foreground content while scrolling."
- **Sequence** — the order of events, explicitly: "first X, then Y, then Z." Controlling
  order = controlling attention; this is what turns motion into direction.
- 3D only when it adds value — it obeys the same reveal/sequence system as everything else.

## Layered workflow (never one giant prompt)
Apply ONE behavior layer at a time, check it live, then add the next:
1. Reveals → 2. Staggers → 3. Hovers → 4. Micro-interactions → 5. Parallax → 6. Sequence tuning.
If a layer breaks, fix THAT layer. Iterate on sensation ("too fast" → describe the feeling
again), not on code. Avoid the classic failure: when everything moves, nothing reads —
motion supports hierarchy, never competes with it.

---

# The refinement loop (Altra Labs — review lesson)

The first generation is a draft, never the verdict. Premium results come from directing
revisions with clarity, not from nailing the perfect prompt.

## Diagnose before re-prompting
Look at the page and NAME the problem first (inconsistent spacing? odd alignment? motion
exaggerated? interaction mushy?). **If you can't clearly explain what's wrong, you can't
clearly ask for what's right.** "Fix this section" forces the AI to guess.

## The fix-prompt pattern
Problem, named + the change + **what must stay intact**:
> "The spacing between headline and paragraph feels too loose and the section feels generic.
> Tighten the spacing and create stronger hierarchy with more white space. **Keep the same
> content and overall layout.**"
Saying what to preserve is as important as saying what to change.

## Classify every problem into ONE layer, fix in this order
**Structure → Motion → Interaction → Polish (visual design) → Performance** — plus **Voice**
(everything works but feels cheap/generic → typography, spacing, hierarchy, color reduction,
visual balance: "reduce color, improve balance, make it feel more intentional").
One layer per prompt. Improving layout can break motion; improving motion can break
interaction — mixed prompts hide which change did what.

## The supervised-change loop
locate → classify → isolate → prompt → preview & compare → **accept or reject** → only then
advance. Every prompt is one supervised change, not "solve everything".

## Refine vs Rebuild
After several failed refinements on the same section, stop insisting:
> "Rebuild the section from scratch keeping the same content. The current structure does not
> match the intended direction."
Refine = the foundation is right, improve it. Rebuild = the foundation is wrong. Knowing
which one you're in saves hours.

## Model tiers
Trivial edits → cheap model · refine/debug → mid model · structural rethink, full motion
choreography, 3D integration → strongest model. Models never replace direction.

## The polish pass (Altra M3 — spacing/balance lesson)
Late stage: structure is done; the work becomes hunting SMALL imbalances, one precise
instruction each, several agents in parallel on different spots.
- **Depth by z-sandwich**: layering type behind AND in front of a visual ("lower 'Becomes'
  behind the structure, upper one in front") is the cheapest real-depth move there is —
  same mechanism as landonorris' helmet-through-type hero.
- **Proportional micro-asks**: "move the text slightly up, extend the bottom by ~25%" —
  refining proportions, never rebuilding. Name section + element + direction + rough amount.
- **Viewport containment**: a featured/showcase section that overflows the viewport height
  breaks its composition — "it must sit fully inside the viewport" is a valid, precise ask.
- **Parallelize the small stuff**: independent micro-fixes can run as simultaneous agents;
  precision of each description is what makes one-shot success (and cheap runs) possible —
  the worse the description, the more re-dos, time and tokens.

## Final QA pass (Altra M3 — closing lesson)
The last stage before calling it done: hunt for anything not smooth, consistent and
INTENTIONAL.
- **Signature details**: even the scrollbar can be designed (twin edge progress bars that
  complete each other at page end) - small authored touches are what make a site feel
  "realized" instead of assembled.
- **Behavior bugs, described precisely**: e.g. a pinned horizontal section must finish ALL
  its horizontal travel before releasing vertical scroll - diagonal drift to the next
  section is a bug. Same expected-vs-observed discipline as always.
- **Collision sweep**: elements overlapping/conflicting across sections (an image from one
  section invading another) - position audits at every breakpoint.
- **Materials to the end**: even nav labels can be treated as material ("engraved directly
  into the marble") - the art direction goes down to the smallest text.
- **The full walkthrough**: scroll the entire site start to finish as a viewer, checking the
  NARRATIVE - every section connects into one story, the composition ends with the same art
  direction it opened with. Only then is it done.

---

*Course status: Altra Labs curriculum (M1–M3) fully absorbed into this skill, 2026-09-05.
The course's own closing point: the value is the PROCESS - turning creative direction into
precise instructions, and diagnosing with clarity. Every brand demands different solutions;
think as a designer creating experiences, not as someone assembling pages.*

*Applied 2026-09-06 (profissional_site): closing-chapter reveal done as the INVERTED variant
(fixed footer beneath a z-raised main + height spacer) because the footer was global to the
layout. Gate: footer fits viewport, lg+, no reduced-motion. Gotcha: the main wrapper MUST
paint an opaque background or transparent sections ghost the footer through mid-page.*
