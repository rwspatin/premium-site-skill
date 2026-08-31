# Kinetic identity site — the landonorris.com / OFF+BRAND recipe

Analyzed 2026-08-31 (https://landonorris.com, by studio OFF+BRAND). This is the bar for a
"personality/brand site" (athlete, founder, creator) — different register from a sales LP:
identity + editorial + play, not conversion anatomy.

## Stack (what they actually use)
- **Webflow** as CMS/layout host + one custom JS bundle (~1.4 MB) by the studio.
- **GSAP full suite**: ScrollTrigger, ScrollSmoother, SplitText, Flip, Observer, CustomEase.
- **Lenis** smooth scroll.
- **Rive** (~20 small canvases per page!): logo mark, buttons, laurel badge, scribble
  underlines, "next race" widget, page-transition overlay. Vector state machines = tiny
  files, crisp at any size, react to hover/scroll inputs.
- **three.js WebGL2 fullscreen canvas** for the hero: wireframe helmet mesh drawn OVER the
  photographic portrait (47 fragmentShader / 416 uniform refs — custom shaders).
- Custom cursor, Klaviyo (email), page transitions isolated in their own Rive bundle.

## The 7 ingredients that make it feel elite

1. **One hero trick, custom-built.** Cutout portrait (transparent PNG/WebP) + a 3D
   wireframe object aligned to the photo (helmet over the head), slowly rotating/scanning.
   The mix of photograph + technical mesh = "human + machine". Our equivalent without a
   3D artist: our WebGL depth-displacement plate (profissional_site) or a wireframe SVG
   drawn with pathLength over the photo.
2. **Acid accent on paper.** Off-white paper bg + ink black + ONE acid color (chartreuse
   #D2FF00-ish) used at full blast on tiny surfaces (STORE button, scribbles, accent
   words). Sections sweep to dark olive/sage full-bleed and back — the palette IS the
   pacing.
3. **Type mixing inside one headline.** Heavy grotesk ALL-CAPS sentence with 1–2 words
   swapped to an editorial serif italic/small-caps in the accent color ("REDEFINING
   limits, fighting for WINS"). SplitText reveals per word. This single move carries most
   of the "designed" feel.
4. **Hand marks as UI.** Scribble underlines, signature SVG, crown/laurel badge — drawn
   strokes (Rive/SVG dash animation) layered on type and photos. Anti-corporate warmth.
5. **Topographic contour background.** Faint topo/contour SVG lines drifting across every
   section (paper AND dark). Generate once (SVG turbulence/contour from noise) and reuse
   as a repeating layer at ~4–8% opacity.
6. **Micro-canvases everywhere.** Every interactive atom (logo, button, badge, widget)
   has its own tiny animation with hover/press states. In our stack: motion/react
   variants + SVG pathLength, or actual Rive files (rive-react runtime is ~40 KB).
7. **Editorial photo journal.** Masonry/scattered grid, mixed sizes, mono caption labels
   ("MIAMI GP, 2024"), duotone-treated archive shots next to full-color hero shots.
   Feels like a zine, not a grid component.

## Replicating in our stack (Next + motion + Lenis)
| Their tool | Ours |
|---|---|
| GSAP ScrollTrigger/SplitText | motion/react useScroll + our SplitText component |
| Lenis | already in profissional_site |
| Rive micro-anims | motion variants + SVG pathLength; adopt @rive-app/react-canvas when a real state machine is worth it (author in rive.app editor, free) |
| three.js hero shaders | our raw-WebGL depth plate (Tier 2) or R3F wireframe mesh (Tier 3) |
| Webflow | Next.js |

## Rules to keep it premium (not chaotic)
- One acid color max; everything else near-neutral. Acid appears < 5% of surface.
- The serif is the guest, not the host: 1–2 words per headline.
- Full-bleed section color changes need 3+ viewports of same background between changes.
- Scribbles/hand marks: max 1 per viewport, drawn once on reveal (no loop).
- Cutout photos need consistent lighting/grade across the site (shoot or grade as a set).
- Everything still ships SSR-visible; canvases and draws are progressive enhancement.
