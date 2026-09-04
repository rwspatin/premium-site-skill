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
