# Recreate this site as a single HTML file: Forma

You are an expert creative front-end developer. Produce a **single self-contained `index.html`**
that reproduces the project below **exactly** — same layout, visuals, motion, and interaction.
Pure HTML/CSS/JS in one file: **no build step, no framework, no bundler**. No libraries are
required at all — the WebGL effect uses the raw WebGL 1 API and the motion uses a small
spring engine you will write inline. Hardcode every value given here as a fixed constant.
Rebuild each component described below as one clearly-delimited section of the single file.
Faithfulness beats brevity — copy the GLSL shaders **verbatim** and bake in every number.

## What it is

Forma is a light, violet-accented **studio contact page** that fills exactly one viewport on
desktop. A full-screen **preloader** plays first: the wordmark top-centre, a giant spring-driven
percentage counter in the middle, and a tall rounded progress bar pinned to the bottom that fills
with a violet→ink gradient; at 100% the bar collapses into its right edge and the curtain fades,
releasing the page. The hero then assembles itself in a choreographed cascade: masthead links rise
in, the two-line heading wipes in **letter by letter**, the name/email contact form rises, the big
centre **video panel** scales down into place — looping footage of a violet lavender valley under a
pale arch and moon — and three cards settle along the bottom (a metric card with a filling gradient
meter, a copy card, and a wide lavender showcase card with light-trail artwork and two circular
arrow controls). Moving the pointer across the video panel spawns **WebGL ripples** — expanding
rings that distort the footage like waves crossing water, leaving a wake behind the cursor. A small
black **custom cursor dot** replaces the native cursor and swells over interactive elements. Every
motion is spring-physics driven. Palette: near-white `#f3f3f5` page, deep ink `#00012f` text,
violets `#8b60ff` / `#9466ff` / `#7751e2` / `#dad7f7` / `#15004f`.

## Page shell & libraries

**No JS libraries.** Raw WebGL 1 for the ripple effect; a tiny inline spring engine for motion
(spec below).

**Font.** General Sans, weights **300 (Light)** and **400 (Regular)**. Load from Fontshare (the
one permitted external `<link>`):

```html
<link href="https://api.fontshare.com/v2/css?f[]=general-sans@300,400&display=swap" rel="stylesheet">
```

Apply site-wide: `font-family: "General Sans", ui-sans-serif, system-ui, sans-serif;`.

**CSS reset & design tokens.** Minimal reset (`* { margin: 0; box-sizing: border-box; }`), then
define these custom properties on `:root` and use them everywhere:

```css
:root {
  --background: #f3f3f5;          /* page */
  --foreground: #00012f;          /* body ink */
  --foreground-strong: #000000;   /* wordmark — deliberately darker than body ink */
  --foreground-inverse: #ffffff;
  --surface-raised: #ffffff;      /* white cards + chip */
  --surface-accent: #dad7f7;      /* showcase card */
  --surface-meter: #f0eff2;       /* meter track */
  --border-subtle: rgba(0,0,0,0.1);
  --border-field: rgba(0,0,0,0.4);
  --border-inverse-subtle: rgba(255,255,255,0.2);
  --accent: #7751e2;              /* chip hover, ring dots */
  --accent-gradient-from: #8b60ff;  /* meter/loader gradient */
  --accent-gradient-to:   #15004f;
  --accent-ring-from: #9466ff;    /* submit button ring gradient */
  --accent-ring-to:   #15004f;
  --radius-card: 1rem;            /* cards & media panel */
  --radius-chip: 3.25rem;         /* pill */
  --radius-meter: 3.125rem;       /* meter bar */
  --text-display: 3.5rem;   /* 56px — heading + loader counter */
  --text-title: 1.5rem;     /* 24px — wordmark */
  --text-heading: 1.25rem;  /* 20px — card titles */
  --text-body: 1rem;        /* 16px */
  --text-small: 0.875rem;   /* 14px */
  --duration-fast: 150ms;
  --duration-normal: 250ms;
  --ease-entrance: cubic-bezier(0.2, 0, 0, 1);
}
body {
  background: var(--background);
  color: var(--foreground);
  min-height: 100lvh;
  overflow-x: hidden;
}
```

**Adaptive root scaling.** The whole layout is in `rem`, and the root font-size scales with the
viewport so the design stays proportional (design base 1440px, 1rem = 16px at 1440):

```css
html { font-size: 16px; }
@media (max-width: 1920px) { html { font-size: 0.833333vw; } }  /* 16*100/1920 */
@media (max-width: 1440px) { html { font-size: 1.111111vw; } }  /* 16*100/1440 */
@media (max-width: 1024px) { html { font-size: 1.5625vw;  } }   /* 16*100/1024 */
@media (max-width: 640px)  { html { font-size: 4.444444vw; } }  /* 16*100/360  */
```

Above 1920px, set an inline font-size from JS on every resize (damped scale-up):
`size = 16 - (16 * ((1920 - innerWidth) / 1920 * 100) * 0.6666) / 100` — i.e. for widths **over**
1920 this grows past 16px at ⅔ proportionality. Below 1920 remove the inline style and let the
media queries drive.

**Scrollbar & scroll model.** Hide the native scrollbar entirely (`html { scrollbar-width: none; }`
plus `::-webkit-scrollbar { display: none; }`). On desktop (`min-width: 1024px`) the hero is exactly
viewport height, so nothing scrolls. While the preloader is up add `overflow: hidden` to `<html>`
(class `is-loading`), removed when it finishes.

## The spring engine (all motion runs through this)

Implement a tiny spring integrator and use it for **every** animation — nothing may use CSS
transitions except the hover states named below. Semantics match react-spring: a spring has
`tension`, `friction`, `mass` (default 1) and animates a value toward a target;
per-frame (dt in seconds, use `requestAnimationFrame`):

```js
force        = -tension * (value - target);
damping      = -friction * velocity;
acceleration = (force + damping) / mass;
velocity    += acceleration * dt;
value       += velocity * dt;
```

A spring is "at rest" when `|value - target| < 0.005` and `|velocity| < 0.005` (then snap to the
target and fire an `onRest` callback if given). Springs animating multiple properties of one
element (opacity + translateY + blur) share one 0→1 progress spring and interpolate each property
from it. Support a per-call `delay` in ms before the target changes take effect.

**Spring configs used (tension / friction / mass):**

| Use | tension | friction | mass |
|---|---|---|---|
| Reveal rise-in (`IntroReveal`) | 150 | 26 | 1 |
| Heading letters | 115 | 24 | 1.1 |
| Word reveals | 140 | 25 | 1 |
| Media panel scale-in | 90 | 28 | 1.2 |
| Meter fill | 90 | 26 | 1 |
| Loader counter (filling) | 46 | 22 | 1 |
| Loader counter (final snap to 100) | 180 | 26 | 1 |
| Loader curtain fade | 95 | 26 | 1 |
| Loader bar collapse | 150 | 28 | 1 |
| Ripple hover intensity | 120 | 26 | 1 |
| Cursor swell/fade | 520 | 34 | 1 |

## The preloader

A fixed full-screen curtain (`position: fixed; inset: 0; z-index: 50;`) with the page background
colour, padding `1.875rem` (30px) on all sides, laid out as a column:

- **Top:** the wordmark `Forma®` centred, `1.5rem` size, line-height 1, colour
  `var(--foreground-strong)`.
- **Middle (flexes):** a huge percentage counter centred vertically and horizontally —
  `3.5rem`, weight 300, line-height 1, colour `var(--foreground)`. Text is
  `` `${Math.round(value)}%` ``.
- **Bottom:** a progress bar the height of the hero's card row: full width, height `8.8125rem`
  (141px), `border-radius: 1rem`, `border: 1px solid var(--border-subtle)`, background
  `var(--surface-raised)`, `overflow: hidden`, **right-anchored** (`margin-left: auto`). Inside,
  a fill div (height 100%, same radius) with
  `background: linear-gradient(to right, #8b60ff, #15004f)` whose **width in %** equals the
  counter value — one spring feeds both so they can never disagree.

**Progress logic.** Constants: `HOLD_AT_FULL = 150` ms, `FADE_DELAY = 110` ms,
`MIN_DURATION = 900` ms, `MAX_DURATION = 7000` ms, `POLL_INTERVAL = 120` ms. Track real load
signals: `document.fonts.ready`, the `window` `load` event, and pre-decoding the showcase image
(each settled signal raises the target by `1/total`). Poll every 120 ms; the displayed progress is
`max(signalsTarget, creep)` where `creep = min(elapsed / 7000, 1) ** 0.65 * 0.9`, capped at `0.97`
— the bar always moves, but never quite finishes on creep alone. When all signals are settled AND
at least 900 ms have elapsed (or 7000 ms regardless), set progress to 100. Drive the counter with
the **(46, 22)** spring while filling and swap to **(180, 26)** for the final run to 100.

**The lift.** When the counter spring rests at 100: wait 150 ms, then simultaneously (a) fire the
page entrance (everything below reveals from this one moment, `T = 0`), (b) collapse the bar — its
**width** springs 100% → 0 with **(150, 28)**; being right-anchored, the left edge travels right,
so it exits in the direction it was filling — and (c) after a further 110 ms delay fade the whole
curtain's opacity 1 → 0 with **(95, 26)**. Remove the curtain from the DOM (and the `is-loading`
scroll lock) once the fade rests. Animate width, not scaleX — scaling would stretch the rounded
ends.

## Layout — the hero (desktop ≥ 1024px)

One `<section>` filling exactly the viewport: `display: flex; flex-direction: column;
height: 100lvh; padding: 1rem 1.875rem 1.875rem;` (30px side/bottom gutters, 16px top). Rows
top-to-bottom:

### 1. Masthead (height `1.5rem`, fixed)

`position: relative`. Three groups positioned absolutely:

- **Wordmark** `Forma®` — top-left, `1.5rem`, line-height 1, colour `var(--foreground-strong)`,
  links to `/`.
- **Primary nav** — absolute, `left: calc((100% - 2.5rem) * 700/1340 + 2.5rem)`. A horizontal list,
  gap `2.5rem`, `1rem` text, line-height 1.2: `Home`, `Services`, `Works`, `About` (hrefs `/`,
  `/services`, `/works`, `/about`). Hover: opacity 0.6, 150 ms, ease `var(--ease-entrance)`.
- **Contact link** — top-right: `Contact`, `1rem`, underlined (`text-underline-position:
  from-font`), same hover.

That `calc()` puts the nav on the same rail as the contact form and wide card below — keep the
formula, not a fixed px value.

### 2. Heading row (margin-top `4.375rem`)

Flex row, `align-items: flex-end; justify-content: space-between`:

- **The `<h1>`** — width `calc((100% - 2.5rem) * 664/1340)`. Two stacked lines, both `3.5rem`,
  line-height 1 (a little breathing room via line-height 1.1 on the reveal boxes is fine — never
  clip overflow, the letter blur must not be cut):
  - Line 1, weight **300**: `Tell us about your project`
  - Line 2, weight **400**: `We reply within a day`
- **The contact form** — width `calc((100% - 2.5rem) * 640/1340)`. Flex row, `align-items:
  flex-end`, gap `0.75rem`: two underlined fields (each `flex: 1`) and a round submit button.
  Each field: a borderless transparent `<input>` (`1rem` text, line-height 1.2, colour and
  placeholder colour both `var(--foreground)`) in a wrapper with `border-bottom: 1px solid
  var(--border-field); padding-bottom: 1.25rem`. Placeholders: `Name` (autocomplete name),
  `Email` (type email). Submitting just prevents default.

**The submit button** (`3.5625rem` square, round): inline SVG, viewBox `0 0 57 57` —

```html
<svg viewBox="0 0 57 57" fill="none">
  <circle cx="28.5" cy="28.5" r="28.5" fill="url(#forma-submit-ring)"/>
  <circle cx="28.5" cy="28.5" r="26.5" fill="var(--background)" class="submit-disc"/>
  <path d="M37.249 20V37H35.749V21.8711L20.0566 37.5645L18.9961 36.5039L33.999 21.5H19.2725V20H37.249Z" fill="var(--foreground-strong)" class="submit-arrow"/>
  <defs>
    <linearGradient id="forma-submit-ring" x1="0" y1="28.5" x2="57" y2="28.5" gradientUnits="userSpaceOnUse">
      <stop stop-color="#9466ff"/><stop offset="1" stop-color="#15004f"/>
    </linearGradient>
  </defs>
</svg>
```

A 2px gradient ring around a page-background disc with a north-east arrow. On hover the disc fades
to opacity 0 (revealing the gradient flood) and the arrow fill flips to white — both 250 ms,
`var(--ease-entrance)`.

### 3. The media panel (the flexible row)

`flex: 1; margin-top: 2rem; position: relative; overflow: hidden; border-radius: 1rem;
border: 1px solid var(--border-subtle);` — it absorbs all remaining height so the 30px gutter
holds at any viewport height. Inside, an absolutely-inset (`inset: 0`) wrapper holds the ripple
video (next section).

### 4. The card row (margin-top `1.25rem`, height `8.8125rem`)

Flex row, gap `1.25rem`. The three cards share the free width in the ratio **350 : 350 : 640**
(use `flex: 350 1 0%` etc.). All cards: `position: relative; overflow: hidden; border-radius:
1rem; border: 1px solid var(--border-subtle); padding: 1.1875rem;` (19px — the 1px border makes
it read as the design's 20px inset).

Each of the two white cards has a header row (`display: flex; justify-content: space-between;
align-items: center`) of the card title (`1.25rem`, line-height 1.1) and a **ring dot** —
a `0.625rem` inline SVG, viewBox `0 0 10 10`, `<circle cx="5" cy="5" r="4.5"
stroke="currentColor"/>`, colour `var(--accent)` — followed by an `<hr>`
(`margin-top: 0.875rem; border-top: 1px solid var(--border-subtle)`).

**Card A — metric (white).** Title `Recall That Sticks`. Then, `margin-top: 1.6875rem`, a
space-between row in `1rem`/1.2: label `Brand Recall` and value `+3.2%`. Under it
(`margin-top: 0.75rem`) the **meter**: a track `0.375rem` tall, full width,
`border-radius: 3.125rem`, background `#f0eff2`, containing a gradient fill
(`linear-gradient(to right, #8b60ff, #15004f)`, same radius) whose width springs 0 → **61.935%**
(= 192/310) with **(90, 26)** once the page enters.

**Card B — copy (white).** Title `What You Are`. Then, `margin-top: 1.875rem`, a paragraph
`0.875rem`/1.2, width `15.875rem`: `Identity, art direction, and web — built to attract, not to
be scrolled past.`

**Card C — showcase (wide, lavender `#dad7f7`, white text).** A full-bleed background image
(the light-trails artwork — see Assets) with `object-fit: cover; object-position: 52% 50%`,
covering the whole card behind the content. Over it: the title `See what we've built for brands
like yours` (`1.25rem`/1.1, width `17.5rem`), and pinned to the bottom edge
(`margin-top: auto`, flex space-between, `align-items: flex-end`):

- **Left — arrow link** (`2.25rem` round, links to `/services`): inline SVG viewBox `0 0 36 36`:

  ```html
  <svg viewBox="0 0 36 36" fill="none">
    <circle cx="18" cy="18" r="17.5" stroke="rgba(255,255,255,0.2)"/>
    <circle cx="18" cy="18" r="17.5" fill="#ffffff" class="arrow-flood"/>  <!-- opacity 0, hover → 1 -->
    <path d="M23.3536 18.3536C23.5488 18.1583 23.5488 17.8417 23.3536 17.6464L20.1716 14.4645C19.9763 14.2692 19.6597 14.2692 19.4645 14.4645C19.2692 14.6597 19.2692 14.9763 19.4645 15.1716L22.2929 18L19.4645 20.8284C19.2692 21.0237 19.2692 21.3403 19.4645 21.5355C19.6597 21.7308 19.9763 21.7308 20.1716 21.5355L23.3536 18.3536ZM13 18V18.5H23V18V17.5H13V18Z" fill="#ffffff" class="arrow-glyph"/>  <!-- hover → fill #00012f -->
  </svg>
  ```

  Hairline white ring, right-pointing arrow; hover floods the disc white and flips the arrow dark
  (250 ms, `var(--ease-entrance)`).

- **Right — chip link** (`All services`, links to `/services`): a pill — white background, radius
  `3.25rem`, padding `0.625rem 1rem`, `0.875rem`/1.2 ink text, leading ring dot (same SVG,
  `0.625rem`, colour `var(--accent)`), gap `0.5rem`. Hover: background → `#7751e2`, text **and**
  dot → white (250 ms).

## The ripple video (WebGL)

Inside the media panel: a muted, autoplaying, looping `<video>` (`playsinline`,
`preload="metadata"`, `object-fit: cover`, absolutely filling the panel) showing the lavender
valley footage (see Assets), with a `<canvas>` absolutely covering it. The video keeps playing
underneath — it is both the texture source and the fallback (if WebGL fails or the context is
lost, remove/hide the canvas and the plain video shows). Keep the canvas at `opacity: 0` until
the first real frame has been uploaded, then show it. Skip the canvas entirely under
`prefers-reduced-motion: reduce`.

**Constants.** `MAX_RIPPLES = 14`, `SPAWN_DISTANCE = 26` (CSS px), `RIPPLE_LIFE = 1.5` s,
unused-slot timestamp `NEVER = -1000`.

**Context & texture setup.** `canvas.getContext("webgl", { alpha: false, antialias: false,
depth: false, stencil: false })`. One full-screen triangle-strip quad
(`[-1,-1, 1,-1, -1,1, 1,1]`, attribute `aPosition`, 2 floats). One texture: seed it with a single
opaque black texel via `texImage2D(..., 1, 1, ..., new Uint8Array([0,0,0]))` so it is complete
before the first video frame (sampling an empty texture flashes green on some drivers).
`CLAMP_TO_EDGE` both axes, `LINEAR` min/mag, `pixelStorei(UNPACK_FLIP_Y_WEBGL, true)`.
Canvas backing size = panel rect × `min(devicePixelRatio, 2)`; on resize (use a `ResizeObserver`)
reset width/height/viewport and **repaint in the same tick** — resizing wipes the buffer and an
unpainted `alpha:false` canvas composites as black.

**Vertex shader (verbatim):**

```glsl
attribute vec2 aPosition;
varying vec2 vUv;
void main() {
  vUv = aPosition * 0.5 + 0.5;
  gl_Position = vec4(aPosition, 0.0, 1.0);
}
```

**Fragment shader (verbatim — `MAX_RIPPLES` is 14, `LIFE` is 1.5):**

```glsl
precision highp float;

#define MAX_RIPPLES 14

varying vec2 vUv;

uniform sampler2D uTexture;
uniform vec2 uResolution;
uniform vec2 uMedia;
uniform float uTime;
uniform float uIntensity;
uniform vec3 uRipples[MAX_RIPPLES];

const float SPEED = 340.0;
const float LIFE = 1.5;
const float WIDTH = 90.0;
const float AMPLITUDE = 24.0;
const float FREQ = 0.055;

vec2 coverUv(vec2 uv) {
  float canvasAspect = uResolution.x / uResolution.y;
  float mediaAspect = uMedia.x / uMedia.y;
  vec2 scale = canvasAspect > mediaAspect
    ? vec2(1.0, mediaAspect / canvasAspect)
    : vec2(canvasAspect / mediaAspect, 1.0);
  return (uv - 0.5) * scale + 0.5;
}

vec2 safeUv(vec2 uv) {
  vec2 guard = 1.5 / uMedia;
  return clamp(uv, guard, 1.0 - guard);
}

void main() {
  vec2 fragment = vUv * uResolution;
  vec2 offset = vec2(0.0);

  for (int i = 0; i < MAX_RIPPLES; i++) {
    vec3 ripple = uRipples[i];
    float age = uTime - ripple.z;
    if (age < 0.0 || age > LIFE) continue;

    vec2 delta = fragment - ripple.xy;
    float dist = length(delta);
    if (dist < 0.001) continue;

    float radius = age * SPEED;
    float envelope = exp(-pow((dist - radius) / WIDTH, 2.0));
    float decay = 1.0 - age / LIFE;
    float wave = sin((dist - radius) * FREQ);

    offset += (delta / dist) * wave * envelope * decay * decay * AMPLITUDE;
  }

  vec2 uv = coverUv(vUv + (offset * uIntensity) / uResolution);
  gl_FragColor = texture2D(uTexture, safeUv(uv));
}
```

`uResolution` is the panel's **CSS-pixel** size (not the DPR-scaled backing size); ripple
positions and the shader's distance math are all in CSS pixels.

**Ripple spawning.** On `pointermove` over the panel (ignore `pointerType === "touch"`): convert
to panel-local coordinates with **y flipped** (`y = rect.height - (clientY - rect.top)` — shader
space is y-up). Only spawn if the pointer travelled ≥ 26px from the last spawn. Write
`(x, y, nowSeconds)` into slot `index % 14`, incrementing `index` — old ripples get overwritten
round-robin. Initialise every slot's timestamp to `-1000`. On `pointerenter` also reset the
last-spawn position to the entry point (so entering doesn't instantly spawn).

**Hover intensity.** `uIntensity` springs 0 ↔ 1 with **(120, 26)** — target 1 while the pointer
is over the panel (movement counts as "over", so a page loaded with the cursor already inside
still activates), 0 on `pointerleave`. The distortion eases in and out rather than snapping.

**Per-frame draw** (every animation frame): upload the video to the texture **only** when
`video.readyState >= 2 && video.videoWidth > 0` and `video.currentTime` changed since the last
upload (uploading during the loop-wrap tick pushes a half-decoded frame — the source of a
periodic blink). Remember the last good `videoWidth/Height` and keep using them for `uMedia` so
the cover-crop can't flip mid-playback. Never draw before the first real frame. Then set all
uniforms (`uTime` = seconds since scene start, `uIntensity` from the spring, `uRipples` as a
flat `Float32Array(42)`) and `drawArrays(TRIANGLE_STRIP, 0, 4)`.

## The entrance choreography (after the preloader lifts, T = 0 ms)

The generic reveal ("rise-in") animates a wrapper from
`{ opacity: 0, translateY: <distance>px, blur: 6px }` to `{ opacity: 1, translateY: 0, blur: 0 }`
with spring **(150, 26, 1)**. Default distance **26px**; masthead items use **14px**, form items
**20px**. Everything holds at its hidden state under the preloader and starts at its delay:

| Element | Delay (ms) | Reveal |
|---|---|---|
| Wordmark | 0 | rise-in, 14px |
| Nav items | 120 + i × 70 (i = 0..3) | rise-in, 14px each |
| Contact link | 120 + 4 × 70 = 400 | rise-in, 14px |
| Heading line 1 | 40 | letters (below) |
| Heading line 2 | 40 + 260 = 300 | letters |
| Media panel | 110 | `{ opacity: 0, scale: 1.12 }` → `{ 1, 1 }`, spring (90, 28, 1.2) |
| Form: Name field | 200 | rise-in, 20px |
| Form: Email field | 290 | rise-in, 20px |
| Form: submit button | 380 | rise-in, 20px |
| Card A (metric) | 280 | rise-in, 26px |
| Card B (copy) | 355 | rise-in, 26px |
| Card C (showcase) | 430 | rise-in, 26px |
| Card A texts | 370 (+120 label / +180 value) | words (below); meter fills at T=0 entry flag |
| Card B texts | 445 title, 565 body | words |
| Card C title | 520 | words |
| Card C arrow link | 740 | rise-in, 14px |
| Card C chip | 820 | rise-in, 14px |

(The pattern: cards start at 280 with a 75 ms stagger; each card's own copy starts 90 ms after its
card; the meter starts filling the moment the page enters.)

**Letter reveal (the heading).** Split each line into letters (preserve spaces). Each letter is an
inline-block span animating from `{ translateX: -34px, opacity: 0, blur: 11px }` to
`{ 0, 1, 0 }`, spring **(115, 24, 1.1)**, with a **20 ms** stagger left-to-right — the cascade
reads as a wipe across the line. Do **not** clip overflow anywhere around the heading: the blur
must be free to spill.

**Word reveal (card titles & copy).** Split into words. Each word animates from
`{ translateY: 26px, opacity: 0, blur: 8px }` to `{ 0, 1, 0 }`, spring **(140, 25, 1)**,
**52 ms** stagger.

## The custom cursor

A fixed black dot (`0.75rem` diameter, `border-radius: 50%`, background
`var(--foreground-strong)`, a 2px white ring via `box-shadow: 0 0 0 2px #ffffff`,
`z-index: 60`, `pointer-events: none`, offset `-0.375rem` on both axes so it centres on the
point). Only on fine pointers without reduced motion: add a class to `<html>` that sets
`cursor: none` on everything (`.has-custom-cursor, .has-custom-cursor * { cursor: none; }`).
Position tracks the pointer **immediately** (no easing — an eased cursor reads as lag); only the
**scale** and **opacity** spring, with **(520, 34)**. Over any `a, button, input, textarea,
select, [role='button']` the dot swells to scale `1.75 / 0.75 ≈ 2.333`; otherwise scale 1.
Opacity 0 until the first move, and back to 0 when the pointer leaves the document or the window
blurs.

## Responsive (below 1024px)

The hero becomes a normally-scrolling column (`min-height: 100lvh`, padding `1.25rem`): masthead
(nav list hidden — only wordmark + contact link), heading and form stack vertically (both full
width, gap `2rem`, heading `2.25rem`, at ≥640px `3rem`), the media panel gets `min-height: 42vh`,
and the three cards stack vertically (gap `1.25rem`, each `min-height: 8.8125rem`). The form
stacks its fields vertically below 640px (gap `1rem`). The desktop composition is the one the
preview video shows — get that pixel-right first.

## Fixed parameters (bake these in)

```js
// Preloader
HOLD_AT_FULL = 150; FADE_DELAY = 110; MIN_DURATION = 900; MAX_DURATION = 7000;
POLL_INTERVAL = 120; CREEP = "min(elapsed/7000, 1) ** 0.65 * 0.9"; CREEP_CAP = 0.97;
// Entrance cues (ms)
CUE = { heading: 40, media: 110, form: 200, cards: 280, cardStagger: 75, cardCopy: 90 };
NAV_STAGGER = 70; HEADING_LINE2_LAG = 260; LETTER_STAGGER = 20; WORD_STAGGER = 52;
// Ripples
MAX_RIPPLES = 14; SPAWN_DISTANCE = 26; RIPPLE_LIFE = 1.5; NEVER = -1000;
SPEED = 340.0; WIDTH = 90.0; AMPLITUDE = 24.0; FREQ = 0.055; DPR_CAP = 2;
// Cursor
CURSOR_SIZE = 0.75 /*rem*/; CURSOR_SIZE_ACTIVE = 1.75 /*rem*/;
// Meter
METER_PROGRESS = 192 / 310;  // 61.935%
// Copy (verbatim)
BRAND = "Forma®";
NAV = ["Home", "Services", "Works", "About"]; CONTACT = "Contact";
HEADING = ["Tell us about your project", "We reply within a day"];
FIELDS = ["Name", "Email"];
METRIC = { title: "Recall That Sticks", label: "Brand Recall", value: "+3.2%" };
COPY = { title: "What You Are", body: "Identity, art direction, and web — built to attract, not to be scrolled past." };
SHOWCASE = { title: "See what we've built for brands like yours", cta: "All services" };
```

## Assets

Two files, loaded from these exact URLs:

- **Hero footage** (the violet lavender valley loop) — the `<video>` source:
  `https://api.getlayers.ai/storage/v1/object/public/public/assets/forma-f8485111e9/purple-valley.mp4`
- **Showcase artwork** (light trails, Card C background image):
  `https://api.getlayers.ai/storage/v1/object/public/public/assets/forma-f8485111e9/showcase-trails.png`

Preload the showcase image as one of the preloader's signals. The video needs only
`preload="metadata"` — it starts playing when ready and the canvas stays transparent until its
first decodable frame.