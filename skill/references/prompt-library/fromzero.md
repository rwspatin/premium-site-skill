# Recreate this site as a single HTML file: Fromzero

You are an expert creative front-end developer. Produce a **single self-contained `index.html`**
that reproduces the project below **exactly** — same layout, visuals, motion, and interaction.
Pure HTML/CSS/JS in one file: no build step, no framework, no bundler. Use ES modules with a CDN
importmap for Three.js 0.180.0. Hardcode every value given here as a fixed constant. Rebuild each
block described below as a section of the one file.

## What it is

A one-viewport hero for **Fromzer(0)**, a fictional creative studio ("We make the web look back").
A dark preloader curtain counts 00→100 and slides away to reveal a night mountain valley rendered
in WebGL: a **video panel showing a human eye floats dead centre**, tilting to face the cursor,
surrounded by floating planes of **live running code** that parallax against it. The valley floor
is a black lake that carries a real-time **analytic reflection of the video panel** and ripples in
the cursor's wake; the terrain reflects it too. Over the canvas sits a poster UI — a frosted
header, a scramble-in headline in a warm brand yellow, and a white CTA bar — that springs in
element by element as the curtain leaves. The camera drifts on a slow dolly, film grain and bloom
finish the image. There is no scroll: the page is exactly one viewport.

## Page shell & libraries

- **Importmap:**

  ```html
  <script type="importmap">
  { "imports": {
    "three": "https://unpkg.com/three@0.180.0/build/three.module.js",
    "three/addons/": "https://unpkg.com/three@0.180.0/examples/jsm/"
  } }
  </script>
  ```

  From `three/addons/`: `loaders/GLTFLoader.js`, `loaders/DRACOLoader.js`,
  `environments/RoomEnvironment.js`, `postprocessing/EffectComposer.js`,
  `postprocessing/RenderPass.js`, `postprocessing/ShaderPass.js`,
  `postprocessing/OutputPass.js`, `postprocessing/UnrealBloomPass.js`.
  Point the DRACOLoader at `https://unpkg.com/three@0.180.0/examples/jsm/libs/draco/`.

- **Fonts** — two self-hosted faces, loaded with `@font-face`:
  - **Chakra Petch Medium** (the display face — logo, headline, counter):
    https://storage.getlayers.ai/assets/fromzero-b5f0a96b6f/ChakraPetch-Medium.ttf
    → `font-family: "Chakra Petch"` weight 500.
  - **General Sans Medium** (the body/UI face — nav, copy, buttons):
    https://storage.getlayers.ai/assets/fromzero-b5f0a96b6f/GeneralSans-Medium.otf
    → `font-family: "General Sans"` weight 500.

- **No scroll.** `html, body { height: 100%; overflow: hidden; }`; the single section is
  `position: relative; height: 100dvh; overflow: hidden; background: #05070b`. The canvas fills
  it absolutely.

- **Adaptive rem grid** — rem-based layout with viewport-scaled root font-size, exactly:

  ```css
  html { font-size: 16px; }
  @media (max-width: 1920px) { html { font-size: 0.833333vw; } } /* 16*100/1920 */
  @media (max-width: 1440px) { html { font-size: 1.111111vw; } } /* 16*100/1440 */
  @media (max-width: 1024px) { html { font-size: 1.5625vw;   } } /* 16*100/1024 */
  @media (max-width: 640px)  { html { font-size: 4.444444vw; } } /* 16*100/360  */
  ```

  The desktop/mobile layout switch (`lg`) is at **1024px**; a smaller type step (`sm`) at 640px.

- **Colour tokens:**

  ```css
  :root {
    --scene-backdrop: #05070b;   /* behind the canvas until it paints          */
    --accent: #ffe289;           /* brand yellow — logo, headline, CTA, arrow  */
    --accent-ink: #121516;       /* near-black ink on the accent + preloader   */
    --header-surface: #707a7c;   /* frosted header glass base (used at 30%)    */
    --duration-fast: 150ms;
    --ease-entrance: cubic-bezier(0.2, 0, 0, 1);
  }
  ```

  Static hovers are CSS transitions (`--duration-fast` + `--ease-entrance`); every entrance is a
  JS spring (below).

- **Spring engine.** All entrance motion uses a small critically-damped-ish spring integrator
  (per-property spring of `mass, tension, friction`, stepped in the shared rAF:
  `a = (-tension·(x−target) − friction·v) / mass`). Implement one `spring(from, to, {mass,
  tension, friction}, delayMs)` helper that animates `opacity` / `transform` / `filter` on an
  element. The reveal config everywhere is **mass 1, tension 46, friction 24** (~1.1 s soft
  settle, no bounce).

## The preloader

A fixed full-viewport curtain (z-index 100), `background: var(--accent-ink)`, padded
`1.5rem 1.875rem`, a column with space-between:

- top-left: **FROMZER(0)** — Chakra Petch, 1rem, uppercase, `color: var(--accent)`.
- bottom row (space-between, align to baselines): a huge counter — Chakra Petch **6rem**,
  white, `font-variant-numeric: tabular-nums`, two digits zero-padded (`00 → 100`) — and the
  word **Loading** — General Sans 1rem uppercase, `rgba(255,255,255,0.4)`.
- a full-width 1px accent progress line (`transform: scaleX(progress)`, `transform-origin: left`)
  pinned at the very bottom of the padding box.

The counter runs 0→100 over **1900 ms** with an ease-in-out-cubic curve. When it rests: flip the
global `revealed` flag (that starts every poster spring and the headline scramble), then slide the
**whole curtain** (content and all) straight up off-screen with a spring — `translateY(-100%)`,
tension 90, friction 20, no opacity fade, no blur — and remove it when settled.

Start the WebGL loading immediately at page load, in parallel with the count. The canvas itself
fades in (opacity 0 → 1, spring tension 120 friction 26) as soon as the scene has prewarmed —
usually while the curtain is still counting, so the reveal shows a live scene, never a pop-in.

## The poster UI (over the canvas)

Root: `position: absolute; inset: 0; z-index: 20; pointer-events: none; user-select: none` — only
the interactive islands re-enable `pointer-events: auto`, so the cursor reaches the canvas
everywhere else.

- **Legibility gradient**: absolute, inset-x 0, bottom 0, height **15.25rem**,
  `background: linear-gradient(to bottom, transparent, rgba(0,0,0,0.7))`.

- **Header** (pointer-events auto): absolute top strip, height **2.4375rem**, full width,
  `background: rgba(112,122,124,0.3)` (`--header-surface` at 30%) + `backdrop-filter: blur(16px)`,
  contents stretched to full height:
  - left: the logo link **FROMZER(0)** — Chakra Petch 1rem uppercase, `color: var(--accent)`,
    `padding-left: 1.875rem`, hover → white.
  - right (≥1024px): nav links **Work · Exhibitions · Stories** — General Sans 1rem, white,
    gap **2rem**, hover → accent; then, after a **7.8125rem** gap, a CTA block **Start a
    project!** — a `width: 11.875rem` full-height centred link, `background: var(--accent)`,
    `color: var(--accent-ink)`, hover → white background.
  - below 1024px the nav and CTA collapse into a burger (two 1.25rem × 2px white bars, gap 5px,
    in a 2.75rem hit area) that toggles a dropdown: absolute `right: 1.25rem; top: 3rem`, width
    **13rem**, column gap 0.75rem, `border: 1px solid rgba(255,255,255,0.15)`, rounded 6px,
    `background: rgba(112,122,124,0.6)` + blur, padding 1.25rem — nav links stacked (white,
    hover accent) and the accent CTA at the foot. It springs in from
    `translateY(-8px) scale(0.96)` + fade (tension 220 friction 24, transform-origin top right);
    the burger's bars cross into an X (±45°, ∓3.5px) on the fast CSS transition.

- **Bottom content block**: absolute, `left/right: 1.875rem; bottom: 1.875rem`, column,
  gap 1rem:
  - overline — **A STUDIO FOR BRANDS THAT MOVE** — General Sans 1rem uppercase,
    `rgba(255,255,255,0.5)`.
  - a row (column below 1024px, gap 1.5rem; row gap **1.875rem** at ≥1024px):
    - **left column** (flex 1, min-width 0, column gap 1.5rem): a 1px full-width divider
      `rgba(255,255,255,0.2)`, then the headline — an `<h1>` in Chakra Petch, uppercase,
      `color: var(--accent)`, `line-height: 1.1`, two explicit `<span>` lines
      (`display: block; white-space: nowrap`): **WE MAKE THE WEB** / **LOOK BACK**. Size
      **4.5rem** ≥1024px, 3rem ≥640px, 1.75rem below.
    - **right column** (width **33.8125rem** ≥1024px, full width below, column gap 1.5rem,
      inner gap **3.375rem** ≥1024px): its own 1px divider (hidden below 1024px); the paragraph
      — **Identity, art direction, and interactive web. AI is the brush — the vision stays
      human.** — General Sans 1rem, line-height 1.2, white, `max-width: 20.1875rem` ≥1024px;
      then the CTA bar (pointer-events auto): a white link bar, height **3.1875rem**, full
      width, `justify-content: space-between`, `padding: 0.25rem 0.25rem 0.25rem 1.5rem` —
      label **Start a project** (General Sans 1rem, black) and a **2.6875rem** square black box
      (`align-items: flex-start; padding-top: 0.75rem`) holding a **1.1875rem** accent arrow
      chevron (SVG path `M6.49963 19L5.57294 18.0733L14.1466 9.49968L5.57363 0.926697L6.50032
      0L16 9.49968L6.49963 19Z` on a 19×19 viewBox). Hover: bar background → accent and the
      arrow nudges `translateX(3px)` on the fast transition.

### The reveal choreography

Nothing plays until the preloader hands off. Then every element springs in with **mass 1,
tension 46, friction 24**, from `opacity 0 / translateY(22px) / blur(8px)` to
`1 / 0 / blur(0)` — except the header, which drops from `translateY(-14px) / blur(6px)`, and the
two dividers, which scale from `scaleX(0.3)` (origin left) + fade. Per-element delays (ms):
**header 100 · headline 120 · overline 260 · left divider 340 · right divider 400 ·
description 560 · CTA 680**.

The headline is a **left→right character scramble** (a decode, not a movement): both lines run
off the same start time; every non-space character cycles random glyphs from
`ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789<>[]{}/=+*#` at ~30 fps and **locks to its true glyph
left→right** — character *i* locks at `i · 42 ms + 260 ms`. The block is laid out at full size
from the start (both lines present, `white-space: nowrap`), so nothing reflows while it decodes.

## The WebGL scene

Renderer: `antialias: true` on desktop only (false on mobile/tablet), `alpha: false`,
`stencil: false`, `powerPreference: "high-performance"` on desktop (default otherwise);
`ACESFilmicToneMapping`, exposure **1**. Camera `PerspectiveCamera(38, aspect, 0.1, 200)` at
`(0, 1, 8)` looking at `(0, 0, 0)`. Scene background and fog are one colour — **#2c3132**,
`Fog(0x2c3132, 8.5, 28.5)`.

**Device tiers** (read once at boot): `mobile` = width < 768 **or**
`(hover: none) and (pointer: coarse)`; `tablet` = width < 1024; else `desktop`. DPR clamp:
mobile `min(dpr, 1)`, tablet `clamp(dpr, 0.75, 1.25)`, desktop `clamp(dpr, 0.75, 1.5)`. Frame
cap: mobile 30 fps, tablet 45 fps, desktop uncapped. Pointer listeners are **not attached** on
mobile. `prefers-reduced-motion`: load everything, render **one settled frame** with the video
parked on a decoded frame (seek to 0.1 s), and never start the loop.

Post chain: `EffectComposer` → RenderPass → **UnrealBloomPass** (strength **0.42** — ×0.55 on
mobile, ×0.8 on tablet — radius **0.84**, threshold **0.15**) → OutputPass → a final **film
grain** ShaderPass in display space:

```glsl
// grain fragment (vertex is the standard passthrough with vUv)
uniform sampler2D tDiffuse;
uniform float uTime;
uniform float uAmount;
varying vec2 vUv;
float hash(vec2 p){ p = fract(p * vec2(123.34, 456.21)); p += dot(p, p + 45.32); return fract(p.x * p.y); }
void main() {
  vec4 c = texture2D(tDiffuse, vUv);
  float g = hash(vUv * 1.71 + fract(uTime) * 97.13) - 0.5;
  c.rgb += g * uAmount;
  gl_FragColor = c;
}
```

`uAmount = 0.06`. Because the OutputPass owns tone mapping + the sRGB encode, every raw
ShaderMaterial below outputs **linear** colour and must not tone-map itself.

### Lighting & environment

One key `DirectionalLight(0xdfe8ff, 1.8)` at `(-6, 9, 5)` + `AmbientLight(0x2a3550, 0.12)` +
an IBL environment: PMREM-process `RoomEnvironment` (blur 0.04) into `scene.environment` with
`scene.environmentIntensity = 0.14`. No other lights, ever.

### Camera sway (a pure dolly)

Each frame the camera **position** drifts on layered sines and the look-at target moves by the
**same** offset, so the view direction never rotates:

```
dx = (sin(t·0.43)·0.6 + sin(t·0.27 + 1.3)·0.4) · 0.18
dy = (sin(t·0.37 + 0.7)·0.6 + sin(t·0.21 + 2.1)·0.4) · 0.12
dz = (sin(t·0.29 + 2.4)·0.6 + sin(t·0.33 + 0.4)·0.4) · 0.16
position = (dx, 1 + dy, 8 + dz);  lookAt(dx, 0 + dy, dz)
```

### The terrain

Load `terrain.glb` (Draco-compressed) from the asset URL below with GLTFLoader + DRACOLoader.
Measure its bounding box once, then normalise: uniform scale = `25.5 / max(size.x, size.z)`,
Y scale ×**1.2** on top; position so the footprint centre lands at `x = +1.6`, `z = −10.1` and
the model's min-Y sits at `−2.8`; rotation.y = **0.09**. On every mesh material
(MeshStandardMaterial): `side = FrontSide`, `roughness 0.4`, `metalness 0`,
`envMapIntensity 0.35`, `color = #2c3132`; if it has a normal map, `normalScale (4, 4)` and
wrap/repeat it **4.8×** both axes. `frustumCulled = false` on the meshes. Inject the panel
reflection (below) into every terrain material.

### The video panel

Create a `<video>` for `eye.mp4` (asset URL below) — `loop muted playsinline preload=auto
crossOrigin=anonymous` — and a `VideoTexture` (`SRGBColorSpace`, linear min/mag filter, no
mipmaps). Give up waiting after 15 s; if autoplay is refused, seek to 0.1 s so the panel shows a
decoded frame, never black.

The panel is a **unit `PlaneGeometry(1, 1)`** with `MeshBasicMaterial({ map: videoTexture,
toneMapped: false })` — the panel is the light source of the composition. It hangs at the world
origin under a **pivot group** at `(0, 0, 0)`; the pivot carries the cursor tilt and parents the
panel, the falling-rectangles overlay and the code planes, so everything tilts together and the
code parallaxes against the panel by depth.

**Sizing:** each resize, size the panel as a share of the visible frustum at its depth —
`height = 2·tan(fov/2)·distance(camera, pivot) · 0.37`, `width = height · videoAspect`, capped
at `visibleWidth · 0.46` (shrink height to keep the aspect if the cap bites). The code-plane
group's uniform scale = the panel height, so the cluster tracks the panel across viewports.

**Cursor tilt:** pointer NDC (x, y ∈ −1..1, y **down**) eases toward the target with a
frame-rate-corrected lerp `k = 1 − (1 − 0.08)^(dt·60)`; then `pivot.rotation.y = ndc.x · 0.34`,
`pivot.rotation.x = −ndc.y · 0.34`.

### The falling-rectangles overlay (over the video)

A second unit quad, **child of the panel mesh** at `z = +0.02`, `renderOrder 2`, additive,
`transparent, depthWrite: false, depthTest: false, toneMapped: false`, `uColor #cfe0ff`,
`uOpacity 0.6` — bright bars that fall down the panel and reseed off-screen:

```glsl
precision highp float;
uniform float uTime;
uniform float uOpacity;
uniform vec3 uColor;
varying vec2 vUv;

float hash(vec2 p){ p = fract(p * vec2(123.34, 456.21)); p += dot(p, p + 45.32); return fract(p.x * p.y); }

void main() {
  vec2 uv = vUv;

  float cols = 26.0;
  float col = floor(uv.x * cols);
  float colBase = hash(vec2(col, 1.0));

  float cx = fract(uv.x * cols) - 0.5;
  float halfW = 0.12 + colBase * 0.28;
  float colMask = smoothstep(halfW, halfW - 0.08, abs(cx));

  float bars = 0.0;
  for (int k = 0; k < 2; k++) {
    float ko = float(k);
    float speed = 0.15 + hash(vec2(col + ko * 11.0, 2.0)) * 0.55;
    float phase = uTime * speed + colBase * 3.17 + ko * 0.5;
    float cyc = floor(phase);
    float local = fract(phase);

    float seed = hash(vec2(col + ko * 31.0, cyc));
    float len = 0.14 + seed * 0.42;
    float on = step(0.4, hash(vec2(col * 1.7 + ko * 7.0, cyc)));

    float headY = (1.0 + len) - local * (1.0 + 2.0 * len);
    float d = headY - uv.y;
    float inBar = step(0.0, d) * step(d, len);
    float bright = mix(0.5, 1.0, smoothstep(len, 0.0, d));
    bars += inBar * bright * on;
  }

  bars *= colMask;
  bars *= smoothstep(0.0, 0.06, uv.y) * smoothstep(1.0, 0.9, uv.y);

  gl_FragColor = vec4(uColor * bars * uOpacity, 1.0);
}
```

### The code planes (running code around the panel)

Six quads (desktop; the first **4** on tablet, first **3** on mobile) in a group under the pivot,
each textured from a **2-D canvas of real monospace text** (`CanvasTexture`, trilinear mipmaps,
anisotropy 8 — 1 on mobile), rendered additively so the glyphs glow
(`transparent, AdditiveBlending, depthWrite: false, toneMapped: false`) through a tint shader:

```glsl
// fragment (vertex = passthrough with vUv)
uniform sampler2D uMap;
uniform float uBrightness;
uniform vec3 uColor;
varying vec2 vUv;
void main() {
  vec3 c = texture2D(uMap, vUv).rgb;
  gl_FragColor = vec4(c * uColor * uBrightness, 1.0);
}
```

`uColor = #ffcf4d` (warm amber), global brightness **0.71** × the per-plane `bright` below.

Layout — positions/sizes in **panel-height units** (the group is scaled by panel height), quad
size `w × h`, per-plane mode/speed/brightness/variant. Positions come from the shipped tuning
(they override the x/y/z of the base layout table):

| # | position (x, y, z) | w × h | mode | speed | bright | variant |
|---|---|---|---|---|---|---|
| 0 | (0, **0.55**, **−0.47**) | 1.5 × 1.5 | scrolling | 1.0 | 0.8 | 0 |
| 1 | (**1.34**, **0.44**, **0.54**) | 0.72 × 0.46 | compiling | 1.0 | 0.95 | 0 |
| 2 | (**−1.59**, **−0.24**, **0.2**) | 0.66 × 0.6 | typing | 1.0 | 0.95 | 0 |
| 3 | (**0.67**, **−0.64**, **0.28**) | 0.66 × 0.42 | typing | 1.15 | 0.85 | 1 |
| 4 | (**1.29**, **0.16**, **−1.57**) | 1.05 × 1.3 | scrolling | 0.85 | 0.4 | 1 |
| 5 | (**−0.9**, **0.66**, **0.32**) | 0.58 × 0.34 | compiling | 0.9 | 0.7 | 1 |

Canvas setup per plane: resolution ≈ `560 · w` px wide × `560 · h` px tall (mobile 380, caps
900/520), font `12px ui-monospace, "SFMono-Regular", Menlo, monospace`, `textBaseline: top`,
char width `7.2px`, line height `15.84px`, **no shadowBlur** — crisp glyphs. Draw in greyscale
levels — body `#cfcfcf`, bright `#ffffff`, dim `#5c5c5c` — the shader supplies the colour.
Redraw each plane on its own cadence (scrolling every 0.05 s, others every 0.09 s) and set
`texture.needsUpdate`.

Three modes:

- **scrolling** — a source file loops upward at `3.2 lines/s · speed`, drawn with a fractional
  line offset, alpha dissolving over the top and bottom **12%** of the canvas. Start each plane
  at a random integer offset (0–39).
- **typing** — a snippet typed out at `26 chars/s · speed` with a blinking `▋` caret (2 Hz);
  when complete, hold 2.6 s, then clear and retype.
- **compiling** — a build log appearing at `2.4 lines/s · speed`, tailing to fit the canvas;
  lines containing `✓` draw bright, the still-appearing line draws dim with the caret; a
  `{bar}` line renders as a progress bar of `█`/`░` glyphs (width = min(24, cols−4), filling at
  0.55/s). When done, hold 2.8 s and restart.

The texts, verbatim — scrolling file for variant 0:

```
import { useState, useEffect } from "react";
import styled from "styled-components";

const Card = styled.div`
  display: flex;
  gap: 12px;
  padding: 1rem 1.25rem;
  border-radius: 16px;
  backdrop-filter: blur(12px);
`;

export function Hero({ title, items }) {
  const [open, setOpen] = useState(false);
  const [data, setData] = useState([]);

  useEffect(() => {
    fetch("/api/items")
      .then((r) => r.json())
      .then(setData)
      .catch(console.error);
  }, []);

  const toggle = () => setOpen((v) => !v);

  return (
    <Card onClick={toggle} data-open={open}>
      <h1 className="title">{title}</h1>
      <ul>
        {items.map((it) => (
          <li key={it.id}>{it.label}</li>
        ))}
      </ul>
    </Card>
  );
}
```

scrolling file for variant 1:

```
:root {
  --bg: #0b0d12;
  --fg: #e8edf5;
  --accent: #6c8cff;
}

* { margin: 0; box-sizing: border-box; }

.grid {
  display: grid;
  grid-template-columns: repeat(12, 1fr);
  gap: clamp(8px, 2vw, 24px);
}

@media (max-width: 768px) {
  .grid { grid-template-columns: 1fr; }
}

const app = createApp(App);
app.use(router).use(store);
app.mount("#root");

async function load(url) {
  const res = await fetch(url);
  if (!res.ok) throw new Error(res.status);
  return res.json();
}
```

typing snippet variant 0:

```
const el = document.querySelector(".cta");
el?.addEventListener("click", () => {
  document.body.classList.toggle("dark");
});
```

typing snippet variant 1:

```
export default function App() {
  const [n, set] = useState(0);
  return (
    <button onClick={() => set(n + 1)}>
      count: {n}
    </button>
  );
}
```

compiling log variant 0:

```
$ vite build
vite v5.2.0 building for prod
transforming modules...
{bar}
✓ 128 modules transformed
dist/index.html    1.2 kB
dist/assets/app.js  142 kB
✓ built in 3.42s
```

compiling log variant 1:

```
$ npm run build
> next build
  linting and checking types
{bar}
  compiled successfully
✓ 12 pages generated
  ready in 5.1s
```

### The panel reflection (terrain + water share it)

The panel is a finite rectangle above irregular surfaces, so the reflection is **analytic**: each
fragment mirrors its view ray about its normal-mapped surface normal and intersects the mirrored
ray with the panel's plane; inside the quad it samples the video, outside it contributes nothing.
Shared GLSL, included by both the terrain patch and the water shader:

```glsl
uniform sampler2D uReflMap;
uniform mat4 uReflPanelInverse;   // inverse world matrix of the panel mesh
uniform mat3 uReflCameraNormal;   // rotation part of camera.matrixWorld
uniform float uReflScale;         // (panel.scale.x + panel.scale.y) / 2
uniform float uReflStrength;
uniform float uReflFalloff;

vec3 reflectQuad(vec3 reflWorldPos, vec3 reflN, float reflRoughness) {
  if (uReflStrength <= 0.0) return vec3(0.0);

  vec3 reflV = normalize(reflWorldPos - cameraPosition);
  vec3 reflR = reflect(reflV, reflN);

  vec3 reflRo = (uReflPanelInverse * vec4(reflWorldPos, 1.0)).xyz;
  vec3 reflRd = mat3(uReflPanelInverse) * reflR;
  if (abs(reflRd.z) <= 1e-4) return vec3(0.0);

  float reflT = -reflRo.z / reflRd.z;
  if (reflT <= 0.0) return vec3(0.0);

  vec2 reflUv = reflRo.xy + reflRd.xy * reflT + 0.5;
  if (reflUv.x <= 0.0 || reflUv.x >= 1.0 || reflUv.y <= 0.0 || reflUv.y >= 1.0) {
    return vec3(0.0);
  }

  vec2 reflEdge = smoothstep(vec2(0.0), vec2(0.14), reflUv)
                * (1.0 - smoothstep(vec2(0.86), vec2(1.0), reflUv));
  float reflMask = reflEdge.x * reflEdge.y;

  float reflDist = reflT * uReflScale;
  float reflAtten = 1.0 / (1.0 + uReflFalloff * reflDist * reflDist);

  float reflFres = pow(1.0 - clamp(dot(-reflV, reflN), 0.0, 1.0), 3.0);
  float reflRough = 1.0 - reflRoughness * 0.55;

  return texture2D(uReflMap, reflUv).rgb
       * reflMask * reflAtten * reflRough * uReflStrength
       * (0.3 + 0.7 * reflFres);
}
```

`uReflStrength = 3.45`, `uReflFalloff = 0.2`, `uReflMap` = the video texture. Updated per frame
after the pivot tilt: `uReflPanelInverse = panel.matrixWorld.invert()` (a copy),
`uReflCameraNormal.setFromMatrix4(camera.matrixWorld)`, `uReflScale = (panel.scale.x +
panel.scale.y) / 2`.

**Terrain injection** — patch each MeshStandardMaterial via `onBeforeCompile`: pass a
`vReflWorldPos` varying from the vertex stage (`(modelMatrix · vec4(transformed, 1)).xyz` after
`#include <worldpos_vertex>`), then immediately before `#include <opaque_fragment>`:

```glsl
outgoingLight += reflectQuad(
  vReflWorldPos,
  normalize(uReflCameraNormal * normal),
  roughnessFactor
);
```

The normal-mapped normal breaks the reflection up on the rock relief for free.

### The water

A **flat unit `PlaneGeometry(1, 1)`**, rotated `−π/2`, at `y = −1.5`, scaled to **90 × 90**,
`frustumCulled = false`, opaque `ShaderMaterial` with `fog: true` (merge `UniformsLib.fog`). The
waves are done **entirely in the fragment shader** — the height field is evaluated per fragment
and a three-tap finite difference perturbs the normal, which drives reflection distortion,
fresnel and specular; the geometry never moves. The cursor stamps expanding wake rings into a
28-slot ring buffer of uniforms. Vertex shader: pass `vWorldPos`/`vWorldXZ` (+ the fog chunks).
Fragment shader, verbatim (plus the two fog includes where marked):

```glsl
#define NRIP 28

uniform float uTime;
uniform float uAmplitude;
uniform float uFrequency;
uniform float uSpeed;
uniform float uPointerRadius;
uniform float uPointerStrength;
uniform float uRipDecay;
uniform vec2  uRipPos[NRIP];
uniform float uRipAge[NRIP];

uniform vec3 uColorDeep;
uniform vec3 uColorShallow;
uniform vec3 uLightDirection;
uniform float uSpecular;
uniform float uOpacity;
uniform float uWaterReflection;

varying vec3 vWorldPos;
varying vec2 vWorldXZ;

// + the reflection uniforms & reflectQuad() from above
// + #include <fog_pars_fragment>

float surfaceHeight(vec2 q, float fade) {
  float s = uFrequency;
  float t = uTime;

  float a = 0.0;
  a += sin(dot(q, vec2( 1.00,  0.30)) * s        - t * 0.9) * 0.50;
  a += sin(dot(q, vec2(-0.40,  1.00)) * s * 1.7  - t * 1.3) * 0.32;
  a += sin(dot(q, vec2( 0.80, -0.60)) * s * 2.7  - t * 1.7) * 0.20;
  a += sin(dot(q, vec2(-0.70, -0.72)) * s * 4.1  - t * 2.2) * 0.12;
  a += sin(dot(q, vec2( 0.20,  0.98)) * s * 6.0  - t * 2.9) * 0.07;
  float h = uAmplitude * a * fade;

  for (int i = 0; i < NRIP; i++) {
    float age = uRipAge[i];
    float life = smoothstep(0.0, 0.3, age) * exp(-age * uRipDecay);
    if (life < 0.003) continue;
    float d = length(q - uRipPos[i]);
    float front = age * uSpeed * 5.0;
    float ring = d - front;
    float env = exp(-ring * ring * 2.2);
    float reach = exp(-d * (1.8 / uPointerRadius));
    h += sin(ring * 2.0) * env * reach * life * uPointerStrength * fade;
  }
  return h;
}

void main() {
  vec2 q = vWorldXZ;
  float dCam = distance(vWorldPos, cameraPosition);
  float fade = 1.0 / (1.0 + dCam * dCam * 0.004);

  float e = 0.06;
  float h  = surfaceHeight(q, fade);
  float hx = surfaceHeight(q + vec2(e, 0.0), fade);
  float hz = surfaceHeight(q + vec2(0.0, e), fade);
  vec2 grad = vec2(hx - h, hz - h) / e;
  vec3 normal = normalize(vec3(-grad.x, 1.0, -grad.y));

  vec3 viewDir = normalize(cameraPosition - vWorldPos);
  float fres = pow(1.0 - clamp(dot(viewDir, normal), 0.0, 1.0), 4.0);
  fres = mix(0.04, 1.0, fres);

  float crest = clamp(h * 1.6 + 0.5, 0.0, 1.0);
  vec3 body = mix(uColorDeep, uColorShallow, crest * 0.5);

  vec3 sky = uColorShallow * 1.6;
  vec3 color = mix(body, sky, fres * 0.7);

  color += reflectQuad(vWorldPos, normal, 0.14) * uWaterReflection * (0.35 + 0.65 * fres);

  vec3 halfway = normalize(uLightDirection + viewDir);
  color += pow(max(dot(normal, halfway), 0.0), 48.0) * uSpecular;

  gl_FragColor = vec4(color, uOpacity);
  // #include <fog_fragment>   ← output stays linear; the OutputPass encodes
}
```

Values: `uAmplitude 0.16`, `uFrequency 0.49`, `uSpeed 0.4`, `uPointerRadius 6`,
`uPointerStrength 0.35`, `uRipDecay 1.6`, `uColorDeep #212121`, `uColorShallow #2c3132`,
`uWaterReflection 2.6`, `uSpecular 0.9`, `uOpacity 1`, `uLightDirection = normalize(-6, 9, 5)`.
All 28 ring ages start at 1000 (dead).

**The wake:** each frame, add `dt` to `uTime` and to every ring age. Raycast the cursor NDC
(flip Y for the raycaster) onto the plane `y = −1.5`; when the hit point has travelled ≥ **0.5**
world units since the last stamp (or is the first), write the hit XZ into the next ring slot
(round-robin) with age 0. A moving cursor lays a trail; a parked cursor lets the sea settle.

### Loading & prewarm

Load the terrain and the video in parallel. Then build the panel + overlay + code planes, and
**prewarm**: `await renderer.compileAsync(scene, camera)`, `renderer.initTexture(videoTexture)`
(and each terrain normal map), then render one throwaway frame — so the reveal never hitches.
Then fade the canvas in and `video.play()` (catch refusal → seek to 0.1 s). Clamp per-frame `dt`
to 0.05 s so a tab-switch return never jumps the clocks. Pause rendering while the tab is hidden.

## Fixed parameters (bake these in)

panel: heightRatio 0.37, maxWidthRatio 0.46, tilt 0.34 rad, pivot (0, 0, 0), tilt lerp 0.08;
terrain: width 25.5, heightScale 1.2, drop 2.8, depth −10.1, x 1.6, rotationY 0.09, color
#2c3132, roughness 0.4, metalness 0, envIntensity 0.35, normalScale 4, normalRepeat 4.8; world
#2c3132; camera y 1, z 8, fov 38, lookY 0, near 0.1, far 200, sway (0.18, 0.12, 0.16); fog
8.5 → 28.5; reflection strength 3.45, falloff 0.2; key 0xdfe8ff × 1.8 at (−6, 9, 5), ambient
0x2a3550 × 0.12, env 0.14, exposure 1; water y −1.5, size 90, amplitude 0.16, frequency 0.49,
speed 0.4, pointerRadius 6, pointerStrength 0.35, decay 1.6, deep #212121, shallow #2c3132,
waterReflection 2.6, specular 0.9, opacity 1, ripple pool 28, ripple step 0.5; code brightness
0.71, speed 1, color #ffcf4d, positions per the table; video-rain opacity 0.6, color #cfe0ff;
bloom 0.42 / 0.84 / 0.15; grain 0.06; preloader 1900 ms; reveal spring 1/46/24, delays
100/120/260/340/400/560/680; scramble step 42 ms, hold 260 ms, 30 fps; UI colours accent
#ffe289, ink #121516, header #707a7c @ 30%, backdrop #05070b.

## Assets

Load each from the public bucket (all under
`https://storage.getlayers.ai/assets/fromzero-b5f0a96b6f/`):

- `terrain.glb` — the Draco-compressed mountain-valley terrain (decoder from the unpkg three
  examples path above).
- `eye.mp4` — the looping eye video shown on the panel (muted, playsinline).
- `ChakraPetch-Medium.ttf` — the display face (weight 500).
- `GeneralSans-Medium.otf` — the body/UI face (weight 500).