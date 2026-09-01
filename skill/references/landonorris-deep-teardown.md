# landonorris.com — deep teardown (OFF+BRAND)

Analyzed 2026-09-01 from the live site + deobfuscated bundle (1.4 MB `lando-by-OFF+BRAND.js`).
Supersedes the surface pass in `offbrand-kinetic-site.md` (keep both: that one is the recipe
summary, this one is the full system).

## 1. The core idea: a DUALITY system, not a "design"

The whole site is one opposition, executed everywhere:

| | ON TRACK (athlete) | OFF TRACK (person) |
|---|---|---|
| Ground | ink black #111112 | paper #ebeee0 / #f4f4ed |
| Type lead | ultra-heavy grotesk caps (Mona Sans) | display serif (Brier) |
| Accent word | acid script overlay (Rive-drawn "ON") | serif italic in muted sage |
| Hero object | real-time 3D helmet (WebGL) | photographic cutout |
| Texture | topo contours | topo contours (shared thread) |
| Data | race widget (RND, circuit, laurel) | sponsor marquee, signature |

Same layout skeleton on both pages; every variable flips. **Lesson: pick ONE brand-true
opposition (for Renan: TECNOLOGIA vs DIREITO/CLÁSSICO) and let it drive ground, type lead,
hero object and accent per section/page — instead of one uniform style everywhere.**

## 2. Tokens (exact)

- Fonts: **Brier** (display serif, licensed) + **Mona Sans Variable** (grotesk; free, GitHub's).
  Only 2 families; weight range does the rest.
- Palette: ink `#111112` `#282c20` `#3b3c38` · paper `#ebeee0 #f4f4ed #dde1d2` · sage neutrals
  `#c8cbbd #b4b8a5 #b9bbad` · **acid `#d2ff00`** (+ olive-acid `#b2c73a` on paper) · orange
  `#ff6b00` (store/commerce only). Acid never fills large areas — buttons, script overlays, one
  word at a time.
- Breakpoints: Webflow defaults (479 / 767 / 991) — nothing exotic; the discipline is per-element.

## 3. Stack & asset pipeline

Webflow (CMS/layout) + custom bundle: **GSAP** (ScrollTrigger, SplitText, Flip, Observer,
CustomEase) + **Lenis** (`lerp: 0.1, wheelMultiplier: 1, syncTouch: true`) + **Rive** + 
**three.js** (GLTFLoader + **DRACO** + **KTX2**, `OrbitControls` dev-only).

3D assets: `helmet-21.glb`, `disco-02.glb`, `tracks/tracks.glb` (circuit outlines!), HDRI
`studio_small_08_1k` in **three grades** (`--dark / --faded / --light`) — same env map, graded
per theme. Small curated set, heavily reused.

## 4. The WebGL system (from shader uniforms)

One fullscreen WebGL2 canvas + the helmet renderer. Uniform inventory tells the story:

- **Helmet material**: `uTextureMatcap + uMatcapIntensity` (matcap lighting = cheap studio
  look, no PBR) + `fresnel` rim + wireframe pass with `uStrokeColor / uStrokeInsetWidth /
  uStrokeOutsetWidth / uIsWireframeAnimating` → the wireframe-over-face hero is a **stroked
  wireframe material animating over the mesh**, not a post effect.
- **Choreography**: `uHelmetRotation, uHelmetHover, uHelmetTransition, uReveal, uProgress,
  uTransitionIn/Out` — every state is a 0–1 uniform tweened by GSAP (never rAF math in shader).
- **Cursor**: `uMouseCoords, uMousePace, uCursorIntensity` — pace (speed), not just position;
  fast mouse = stronger distortion.
- **Projection**: `uProjectorMatrix/uProjectorViewMatrix` — projecting a texture (livery/logo)
  onto the mesh from a virtual projector.
- **Per-section grading**: DOM attrs `gl-change-from/to/track/trigger-start` → ScrollTrigger
  drives canvas color grade between sections (`uColor`, `uDarkEdges`, `uBrightness`). The
  background is a *shader*, so theme shifts are continuous, not CSS swaps.

## 5. The Rive system (15 instances, ~8 files on home)

Files → artboards (state machines): `reef.riv` → helmet-reef (`_play`, `_scroll`), off-icons ·
`signature.riv` → signature (`signature_play`, `signature_scroll`) · `phrases.riv` → phrase_on,
collabs (`page_home`) · `btn-ui.riv` → arrow (every CTA!) · `circuits.riv` · `ln4.riv` (logo) ·
`mob-landscape.riv` · `page-transition.riv` (isolated bundle so transitions never wait).

Declarative wiring via attributes: `data-rive-file/artboard/state-machine/fit` +
`data-rive-scrolltrigger-start/end/target` (scroll scrubs a state-machine input) +
`data-rive-input-track/weight/color` (page context feeds inputs — e.g. current race track) +
`data-rive-hover`. **Lesson: one `arrow` artboard reused in every button = the whole UI feels
authored for the cost of one file.**

## 6. Motion grammar (measured)

- Ease distribution: `power2.out` (36×) dominates; `power2/1.inOut` for moves; `expo.inOut`
  (12×) for big reveals; `elastic.out(1, 0.75)` (5×) ONLY on playful micro-elements.
- Durations: 0.3–0.6 s for UI (0.5 modal), 1–2.5 s for scene moves. Staggers tiny: 0.015–0.05
  (SplitText letters), never 0.1+.
- ScrollTrigger: `scrub: true` (27×) — almost everything scroll-tied is *scrubbed*, not
  triggered; entries at `top bottom` / `top 90%`. Pin+scrub for scene sections.
- Lenis lerp 0.1 + scrubbed triggers = the "everything glides" feel.

## 7. The architectural lesson: a declarative attribute framework

The bundle is a generic engine reading ~60 custom data-attributes: `marquee-*` (speed,
direction, scroll-speed, duplicate), `nav-theme(-target)`, `footer-theme`, `h-color-from/to`,
`gl-*`, `sticky-hero`, `horizontal-section`, `oval-scroll`, `otot-*` (the ON/OFF split screen),
`home-swipe-*` (mobile gesture UI), `stream-*` (video), `anim(-high)`, `components`.
Designers compose pages in Webflow by tagging elements; the engine does the rest.
**Port idea: our equivalent = typed React components (`<Marquee speed>`, `<ThemeSection>`,
`<StickyHero>`) — we already do this; the gap is coverage, not architecture.**

## 8. Mobile is a different product

Mobile home = full-bleed portrait + **"TAP TO LOCK"** button (dedicated touch interaction,
`home-swipe-*`), landscape gets its own Rive (`mob-landscape.riv`) telling you to rotate.
Nothing is "the desktop, squeezed": interactions are re-designed per input. (Matches our
`responsive-doctrine.md` rule 4 — they go further: bespoke mobile features.)

## 9. Port plan for our stack (Next + motion + Lenis + raw WebGL)

Tiered, each shippable alone:
1. **Duality system** (code only): define the TECH↔LAW/CLASSIC opposition as tokens
   (ground/type-lead/accent per register) and alternate sections/pages. Biggest conceptual win.
2. **Scrub-first motion** (code only): move key reveals from triggered to scrubbed
   (`useScroll` + `useTransform`), Lenis lerp 0.1. Staggers at 0.02–0.05.
3. **Arrow micro-anim reused in every button** (1 Rive file or SVG variants) + page-transition
   overlay.
4. **Shader ground**: extend our depth-plate canvas to full-viewport with per-section color
   grading (uColorFrom/To scrubbed) — continuous theme shifts.
5. **Hero object**: needs an asset — either a photo shoot (cutout, like OFF) or a small GLB
   (scales of justice / column capital / keyboard?) with matcap + wireframe-stroke material
   (uniforms above are the recipe) — this is the ON-equivalent.

## Implementation log
- 2026-09-01 · profissional_site: tiers 1–2 shipped. `.display-grotesk` (sans 700, caps,
  -0.02em, lh .98; `em` inside resets to serif italic opsz 144) on all dark-section headers
  with one brass accent word; paper sections stay serif-led. `Scrub` component (useScroll
  offset ["start 96%","start 55%"] → y 48→0, opacity .12→1, reversible, SSR-visible) replaced
  once-fired reveals on section headers. Lenis lerp 0.1; staggers 0.03/0.05/0.08.
