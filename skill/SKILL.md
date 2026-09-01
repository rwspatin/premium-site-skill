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
| **Immersive hero** | any register's hero needing depth/"3D" | `references/immersive-3d-hero.md` |

Cross-register mandate: `references/responsive-doctrine.md` — mobile is designed, not inherited; every breakpoint utility has a base pair; verification always includes 390px.

Tokens always start from `references/design-system-blueprint.md` — never improvise values.

## Shared workflow
1. **Copy first** (`/copywriting`; `/viral-product-strategist` if the offer is weak).
2. **Tokens** from the Blueprint (dark glass or light paper `:root`).
3. **Build** with `frontend-design`; components re-skinned to tokens (21st.dev / Magic UI / SkiperUI allowed, never raw).
4. **Motion**: CSS reveal for static HTML; `motion-foundations` → `motion-patterns` for React. GSAP/Lenis only when cinematic scroll is the brief.
5. **Polish/QA**: `impeccable` critique → `ui-review` → `accessibility-compliance` → `seo`.
6. **Verify by looking**: follow `references/responsive-doctrine.md` — 390/768/1440 screenshots with the changed section scrolled into view, step-scroll audit at 390 for full pages, no horizontal overflow, Lighthouse mobile. Content must be SSR-visible (reveals never gate visibility). **Desktop-only verification is a bug.**

## Performance budget (premium = light; heavy kills the illusion)
- **Weight**: hero image ≤ 250 KB (one 1200–1600w WebP/AVIF + srcset); total above-fold ≤ 600 KB; video hero ≤ 8 MB with poster; fonts: 2 families max, subset, `display: swap`.
- **Runtime**: one rAF loop max, paused when tab hidden or element offscreen (IntersectionObserver); no permanent `mix-blend-mode` overlays on `position: fixed` full-viewport layers (grain: use a small tiled PNG at low opacity WITHOUT blend, or cap at 3–4% and test scroll FPS); ≤ 6 elements with `backdrop-filter`, blur ≤ 12px, none on mobile scroll paths; transforms/opacity only — never animate layout.
- **LCP** ≤ 2.5 s: backplate/poster gets `fetchpriority="high"`; canvases/shaders/Rive are progressive enhancement that fade in AFTER first paint; everything readable with JS off.
- **Measure, don't guess**: `npx lighthouse <url> --preset=desktop` before claiming done; check total transferred in the network panel at 390px too.

## Micro-motion (the OFF+BRAND feel, cheap)
Every interactive atom gets a state (hover/press/reveal), via motion variants or SVG
`pathLength` draws. Adopt `@rive-app/react-canvas` (~40 KB) only when a real state machine
earns it. Scribbles/hand-marks: 1 per viewport, drawn once on reveal.

## Evolution log
This repo is living: every premium site we dissect becomes a reference file here.
Add teardowns to `references/`, link them in the table above, keep diffs small.
