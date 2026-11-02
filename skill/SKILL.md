---
name: premium-site
description: Build premium, "expensive-looking" websites — sales/landing pages (GRIGOLETTO style), personality/identity sites (landonorris.com / OFF+BRAND level), and immersive "3D" heroes (video, CSS-3D layers, WebGL depth-map, R3F) — on the Design System Blueprint tokens, with a hard performance budget. Use for: landing page, página de vendas, LP, site pessoal/de marca/atleta/fundador, hero 3D, site premium, "site caro", or evolving an existing site toward this bar.
---

# Premium Site

One skill, three registers. Pick the register first, then follow its reference. Everything
"expensive" comes from **restraint + coherence + one custom trick**, never from stacking libraries.

| Register | When | Reference |
|---|---|---|
| **Sales LP** | product/pack/course/infoproduto, conversion anatomy | `references/sales-lp-grigoletto.md` (+ extracted CSS) |
| **Identity site** | person/brand/athlete/founder, editorial + play | `references/offbrand-kinetic-site.md` + deep dive: `references/landonorris-deep-teardown.md` |
| **Immersive hero** | any register's hero needing depth/"3D" | `references/immersive-3d-hero.md` · R3F/no-Blender layer: `references/webgl-r3f-doctrine.md` |

Prompt library: `references/prompt-library/INDEX.md` — five complete single-file recreation prompts (with GLSL and exact values) + preview videos; study/scaffold material and the fidelity bar for our own prompts.
Animation references: `references/prompt-motion-gallery.md` — prompt-motion.com catalog (Opus 5.5 motion videos + prompts/skills) by technique: UI morphs, kinetic type, easing, styles, explainers, 3D; the 6-block motion prompt and its Banned line; how the moves port to web motion.

Cross-register mandates: `references/design-world-never-protocol.md` (Design World sentence + 8–12 concrete NEVERs written before code; blind screenshot-only critique + code scan before editing; fix at token level) · `references/ai-prompting-protocol.md` (build plan → 5-part prompt: context·stack·structure·behavior·constraints; refine one section at a time) · `references/build-plan-protocol.md` (reference curation: 3-question test, nameable blocks, ordered plan) · `references/motion-teardown-protocol.md` (how to dissect any reference: sequence · trigger · timing · easing · repetition) · `references/liquid-glass.md` (glass/refraction recipes — pick by what is behind the glass) · `references/responsive-doctrine.md` — mobile is designed, not inherited; every breakpoint utility has a base pair; verification always includes 390px. · `references/gates-floor-reflex-finish.md` (QA tiers: Floor never waived · Reflex waived only with a logged one-line reason · Finish answered; redesign kept-contract diff) · `references/ai-tell-lint.md` (grep/DOM-measurable AI-slop and template-decoration tells, via agent-browser) · `references/motion-page-contract.md` (`window.anim.seek(t)` / `?t=` / `data-ready` for deterministic renders; seamless-loop math; frame-diff vs source video) · `references/color-derivation.md` (sample → converge → justify the Blueprint's one accent slot in OKLCH; Blueprint stays the token source).

Tokens always start from `references/design-system-blueprint.md` — never improvise values.

## Shared workflow
**NEW projects:** run the `design-directions` skill first (`~/.claude/skills/design-directions/SKILL.md` — six directions → three built → user picks), then step 0 with the picked direction.
0. **Open the project right**: `references/project-direction-protocol.md` - positioning (who/
   for whom/what feeling) → direction file (PRODUCT.md/DESIGN.md) → hero first → page as
   chapters planned before built.
0.5 **Declare the world**: `DESIGN_WORLD.md` (one-sentence world, principles, closed tokens,
   NEVER list) — `references/design-world-never-protocol.md`. On an existing site, run the two
   isolated critiques against it BEFORE editing.
1. **Copy first** (`/copywriting`; `/viral-product-strategist` if the offer is weak).
2. **Tokens** from the Blueprint (dark glass or light paper `:root`).
3. **Build** with `frontend-design`; components re-skinned to tokens (21st.dev / Magic UI / SkiperUI allowed, never raw).
4. **Motion**: CSS reveal for static HTML; `motion-foundations` → `motion-patterns` for React. GSAP/Lenis only when cinematic scroll is the brief.
5. **Polish/QA**: blind critique vs the NEVER list + `impeccable` critique → `ui-review` → `accessibility-compliance` → `seo`.
6. **Verify by looking**: follow `references/responsive-doctrine.md` — 390/768/1440 screenshots with the changed section scrolled into view, step-scroll audit at 390 for full pages, no horizontal overflow, Lighthouse mobile. Content must be SSR-visible (reveals never gate visibility). **Desktop-only verification is a bug.**

## Performance budget (premium = light; heavy kills the illusion)
- **Weight**: hero image ≤ 250 KB (one 1200–1600w WebP/AVIF + srcset); total above-fold ≤ 600 KB; video hero ≤ 8 MB with poster; fonts: 2 families max, subset, `display: swap`.
- **Runtime**: one rAF loop max, paused when tab hidden or element offscreen (IntersectionObserver); no permanent `mix-blend-mode` overlays on `position: fixed` full-viewport layers (grain: use a small tiled PNG at low opacity WITHOUT blend, or cap at 3–4% and test scroll FPS); ≤ 6 elements with `backdrop-filter`, blur ≤ 12px, none on mobile scroll paths; transforms/opacity only — never animate layout.
- **LCP** ≤ 2.5 s: backplate/poster gets `fetchpriority="high"`; canvases/shaders/Rive are progressive enhancement that fade in AFTER first paint; everything readable with JS off.
- **Measure, don't guess**: `npx lighthouse <url> --preset=desktop` before claiming done; check total transferred in the network panel at 390px too.

## Motion doctrine: Guide · Connect · Respond (Altra Labs M2-A3)
Every animation must do at least one of three jobs, or it gets cut:
- **Guide** — direct attention: stagger the hierarchy (headline → support → details), never
  everything at once. The reveal order IS the visual hierarchy.
- **Connect** — show continuity: section transitions, progress, page veils. Same rhythm
  repeated per section = predictability; after 2-3 sections the user "knows" the site.
- **Respond** — confirm action: hover/press/focus/expand/submit each gets a small state
  change. Dozens of tiny consistent responses > one big effect.
Timing: short and fast for feedback; slower only for large transitions/impact moments.
An opening sequence may build anticipation but never makes the user wait for content.
Premium = simple moves (fade/slide/small scale) + controlled timing + one shared direction;
if every element moves differently, motion competes with content.

## Micro-motion (the OFF+BRAND feel, cheap)
Every interactive atom gets a state (hover/press/reveal), via motion variants or SVG
`pathLength` draws. Adopt `@rive-app/react-canvas` (~40 KB) only when a real state machine
earns it. Scribbles/hand-marks: 1 per viewport, drawn once on reveal.

## Evolution log
This repo is living: every premium site we dissect becomes a reference file here.
Add teardowns to `references/`, link them in the table above, keep diffs small.
