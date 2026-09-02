# Liquid Glass (Apple-style refraction) — production paths

Evaluated 2026-09-02 from reel indications (react-three-fiber · liquid-glass-js · framer-motion
· ui-ux-pro-max · 21st.dev — the last three already in our stack; R3F is already Tier 3 of
`immersive-3d-hero.md`).

## Verdict on `dashersw/liquid-glass-js` (751★)
Real WebGL refraction with nested glass sampling — but it **samples the page via html2canvas**:
re-renders the DOM to a canvas on load/changes. Slow, breaks on cross-origin images, fights
hydration, and busts our performance budget. **Good to study, do not ship.** The same trap
applies to any "glass that refracts arbitrary DOM" library.

## The three production paths (pick by what's BEHIND the glass)

1. **Behind = flat/unknown DOM** → classic CSS glass (what we already ship):
   `backdrop-filter: blur(10-16px) saturate(1.2)` + hairline border + top edge highlight
   (`inset 0 1px 0 rgba(255,255,255,.12)`). Cheap, robust, 90% of the read.
   Optional Apple-y upgrade: SVG displacement on the backdrop —
   `backdrop-filter: url(#lens)` with `feImage` (radial displacement map) + `feDisplacementMap`.
   Chromium-only today; feature-detect and fall back to blur. One filter def, zero JS.
2. **Behind = a texture WE control** (image/canvas hero, like our DepthPlate) → true refraction
   for free: render the same texture again inside the card's clip with displaced UVs
   (`uv + normal(edge) * strength`, magnify ~1.04 at center, chromatic offset ±0.002 optional).
   This is the premium path — real lens, no page sampling, ~15 lines of shader.
3. **Behind = 3D scene** → R3F `MeshTransmissionMaterial` (drei): thickness/roughness/chroma.
   Only when a WebGL scene already exists (Tier 3); never add three.js just for glass.

## Rules
- Glass is rare and purposeful (absolute ban on decorative default glass stands).
- Every glass surface needs: readable contrast on its content (≥4.5:1 vs the *blurred* result),
  a fallback (`@supports not (backdrop-filter: blur(1px))` → solid tint), mobile blur ≤ 8-10px.
- Refraction strength: barely-there. If you can screenshot it and immediately see distortion,
  halve it. Apple's read is 2-3px of edge bend, not a funhouse mirror.
- Reference for lens-over-carousel done well: Yousuf-developer/liquid-glass-carousel
  (three.js + GSAP, 143★) — study the shader, not the stack.
