# WebGL / R3F doctrine — 3D without Blender

Curated 2026-09-03 from a GPT-drafted "Premium Web Experience Skill" (user-supplied),
deduplicated against our existing doctrines. Everything already covered elsewhere
(DOM-first, perf budget, motion Guide/Connect/Respond, responsive doctrine, device tiers,
AI-cliché bans) is NOT repeated here — this file is the 3D-specific layer.

## Blender-free 3D (the point)
Most premium web 3D needs NO external modeling app:
- **Procedural geometry in code**: icosahedron/tube/plane/ring + vertex displacement or
  shaders → abstract sculptures, waves, grids, particles, terrain. Often looks MORE premium
  than imported models.
- **Drei primitives** before hand-rolling: `Float`, `Environment`, `ContactShadows`,
  `MeshTransmissionMaterial`, `Sparkles`, `Instances`, `Text`, `PresentationControls`.
- **Spline** only when visual authoring beats code (abstract comps, logo extrusion);
  export ≠ full fidelity — for scroll/shader control prefer pure R3F. Spline accelerates
  art direction, never constrains architecture.
- **GLB import** only for genuinely complex objects; then ALWAYS through the pipeline below.
- External modeling (Blender) only for organic/character work — out of scope by default.

## Decision trees (memorize)
Effect: **CSS → GSAP/motion+DOM/SVG → R3F** (needs real depth/material/lighting?) → else
reconsider. Object: simple → procedural · art-directed abstract → Spline/procedural ·
complex → GLB · organic/character → external pipeline.

## Scene architecture (R3F)
- One `<Canvas>` per page, persistent when the object recurs across sections (never
  destroy/recreate renderers). DOM owns all content; canvas is a visual layer.
- Components, not a giant Scene.tsx: `three/{Scene,CameraRig,Lighting}` +
  `objects/ materials/ effects/` + `shaders/<name>/{vertex,fragment}.glsl` +
  `animations/<x>Timeline.ts` (timelines extracted from presentation).
- **State split**: React state for app state (variant, menu); refs/animation-lib values for
  per-frame data (rotation, uniforms, scroll lerp). Never setState per frame.
- `useFrame` only for genuinely continuous motion (time uniforms, damping, particles);
  no allocations inside; choreography belongs to timelines, not useFrame ifs.

## Animation engine note (adjusted to OUR stack)
The source doc says GSAP-first. Our rule: **motion/react stays primary on React sites**
(tokens + SSR-visible doctrine already built on it); adopt GSAP+ScrollTrigger only for
cinematic scrub choreography a single timeline orchestrates better (camera+object+DOM in one
scrubbed/pinned sequence), or on static HTML. If Lenis + GSAP coexist: drive Lenis from
GSAP's ticker — never two rAF loops. One page = one animation engine wherever possible.

## Camera & lighting (the cinematographer rules)
- Camera moves communicate narrative: slow dolly/push-in/pull-out; mouse influence ±2–5%,
  damped, never raw. Map sections → camera states, interpolate via timeline (wide → medium
  → close-up → pass-through → final wide).
- Lighting beats geometry: HDRI environment + key + fill + rim. Luxury = dark env,
  controlled highlights, high material contrast. Every light has a purpose.
- Post-processing = finishing: subtle bloom + tone mapping + very light noise. Never stacks.
- Materials: PBR, and no transmission/glass everywhere (see liquid-glass.md).

## Performance (3D-specific, extends the perf budget)
- `dpr={[1, 1.5]}` cap; raise only with visible benefit.
- Shadows: baked/contact/AO first; dynamic high-res shadows are a budget item.
- Instancing for anything repeated; merged static geometry; LOD.
- Quality tiers: high/medium/low (particles 50k/15k/3k, DPR 1.5/1.25/1, effects on/limited/off)
  picked by device heuristic; mobile gets a DESIGNED composition (new camera/FOV/scale per
  breakpoint, not a scaled-down desktop) or an art-directed poster fallback — never "broken".
- Loading: HTML shell → typography → critical assets → canvas fades in. No loader screens
  unless the site IS an immersive app. Preload only the hero GLB/texture.

## Asset pipeline (when GLB is used)
`gltf-transform inspect in.glb` → `gltf-transform optimize in.glb out.glb
--texture-compress webp` (Draco/Meshopt for mesh, KTX2/Basis when GPU compression pays).
Preserve node hierarchy for animated models; always eyeball the optimized result.
Textures sized to rendered pixels: hero ≤2K, small 512–1K, detail 256–512. `useGLTF.preload`.

## WebGPU / TSL / Theatre.js
Opt-in only: WebGPU for compute/large particle sims (different architecture; needs fallback).
Theatre.js only when a visual keyframe editor genuinely helps (cinematic multi-object
choreography); GSAP/motion covers scroll-driven work.

## Definition of done (3D additions to our checklist)
3D is necessary (not decoration) · readable without WebGL · reduced-motion kills camera
moves/scrub · optimized assets · cleaned-up timelines/triggers on unmount · single rAF ·
smooth on mid hardware · mobile intentionally composed.

## Implementation log
- 2026-09-03 · profissional_site hero: procedural ionic wire column (LineSegments, ~1.6k
  segments: fluted shaft w/ entasis, archimedean volutes, abacus edges), brass
  AdditiveBlending opacity .55, drift + damped pointer tilt (touch: sin/cos sweep).
  next/dynamic ssr:false + unmount when hero >300px offscreen (useInView gate) →
  zero cost below the fold. Rendered OVER the photographic backdrop as a living
  technical drawing - complement, not replacement.
- 2026-09-04 · v2 lessons (after "ficou simples e solto" feedback): (1) raw uniform wireframe
  reads as debug output — craft = line hierarchy + depth fade + partial dissolve + a scan
  band (time uniform), not more geometry; (2) a floating 3D object must be ANCHORED to the
  composition (here: survey marks annotating the photographed stone; oscillation, never full
  spin, to hold the alignment); (3) **additive-blended lines vanish over bright imagery** —
  double-pass ink (normal blending) under glow (additive) reads on any backdrop.
- 2026-09-06 · v3 (Playwright box-audit round): alignment is MEASURED, not eyeballed —
  getBoundingClientRect every art element, find shared edges (or their absence). Lessons:
  (1) when overlaying 3D on a photo, match the photo's perspective — a near-frontal photo
  tolerates ≤4° of drawing rotation before the two systems visibly fight; (2) less geometry,
  more anchoring: ONE surveyed volute aligned to the real one beats a whole column floating;
  (3) R3F gotcha: any channel useFrame writes (e.g. position.y for a bob) silently overrides
  the declarative prop — bake the base value into the useFrame expression;
  (4) decorative plates need a shared alignment grid (common right edge) or they read random.
