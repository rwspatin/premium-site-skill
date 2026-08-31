# Immersive "3D" hero — how sites like the GRIGOLETTO Figma-templates LP get that depth

Reference site: https://figma-templates-lp.vercel.app/ (analyzed 2026-08-29).
**Reality check:** the LP itself has ZERO 3D code (no `perspective`, no WebGL, no three.js).
The wow factor is a 1920×1080 `<video autoplay muted loop playsinline>` in the hero
(`edit hero lp templates.mp4/.webm`, 20s, 7 MB) — a screen-recording of Figma prototypes.
The LP wrapper is the standard premium-lp anatomy (hero → #pack → #galeria → #para-quem
→ #como-funciona → #preco → #faq), dark tokens, DM Serif Display + Inter, glass cards.

So "that beauty" = **(A) the LP shell (already covered by this skill)** + **(B) an
immersive scene inside the media slot**. This file is about (B).

## 1. Anatomy of the immersive scene (what the eye reads as "3D")

From the video frames, every scene is the same recipe:

1. **Photographic AI environment as the backplate** — fantasy/dreamlike (forest portal
   opening to pastel sky; cotton-candy clouds at sunset). Cinematic DOF: sharp mid,
   blurred foreground foliage, hazy far distance. This alone carries ~70% of the effect.
2. **Editorial display type on top** — thin serif caps with stylized glyphs
   ("STEP › INTO WONDER", "CREATE BEYOND REALITY"), centered, light color, tight tracking.
3. **Floating UI cards in perspective** — 4–6 rounded pastel/glass cards, each with its
   own `rotateY/rotateZ`, slightly different scale (near = larger, sharper; far = smaller,
   softer, lower opacity). They read as signposts stuck in the scene.
4. **Motion = camera drift, not UI animation** — slow zoom-in on the backplate, cards
   sliding as a horizontal carousel with parallax (near cards move more than far cards),
   scroll indicator pill, pagination dots. Everything eased, nothing bounces.
5. **Atmosphere** — soft vignette, glow bloom near light sources, mist layer, grain.
   Contrast is kept low so the type and glass cards pop.

Rule: **depth comes from the image + scale/blur/opacity hierarchy, not from meshes.**

## 2. Three implementation tiers (pick the cheapest that sells it)

### Tier 0 — Video hero (what the reference does)
Render the scene elsewhere (Figma Smart Animate screen-record, After Effects, Remotion
in `~/git/personal/marketing`) and drop it in:
```html
<div class="hero-media"><video autoplay muted loop playsinline preload="metadata"
  poster="assets/hero-poster.webp">
  <source src="assets/hero.webm" type="video/webm">
  <source src="assets/hero.mp4"  type="video/mp4"></video></div>
```
- ≤ 8 MB, 1080p, 20–30 s loop, H.264 + VP9/AV1. Poster = first frame (LCP + stills).
- Pros: zero runtime cost, pixel-perfect. Cons: not interactive, no dynamic copy.
- Use for: sales pages where the product *is* visual (templates, packs, motion reels).

### Tier 1 — CSS 3D layers (no libs, interactive, 90% of the look)
One backplate + N absolutely-positioned cards inside a `perspective` stage. Mouse/scroll
drives a small rotation of the whole stage; layers at different `translateZ` parallax
automatically because the browser projects them.
```html
<section class="scene" style="--rx:0deg;--ry:0deg">
  <img class="plate" src="assets/scene.webp" alt="">
  <div class="stage">
    <article class="card" style="--z:-260px;--x:-38%;--y:8%;--rz:-9deg">…</article>
    <article class="card" style="--z:-120px;--x:-14%;--y:0;--rz:-4deg">…</article>
    <article class="card card--near" style="--z:40px;--x:12%;--y:-2%;--rz:3deg">…</article>
    <article class="card" style="--z:-200px;--x:40%;--y:6%;--rz:8deg">…</article>
  </div>
  <div class="mist"></div><div class="vignette"></div>
</section>
```
```css
.scene{position:relative;aspect-ratio:16/9;overflow:hidden;border-radius:var(--r-xl);isolation:isolate}
.plate{position:absolute;inset:-6%;width:112%;height:112%;object-fit:cover;
  transform:translate(calc(var(--ry)*-.6),calc(var(--rx)*.6)) scale(1.04);
  transition:transform .6s var(--ease-soft);animation:drift 28s ease-in-out infinite alternate}
@keyframes drift{to{scale:1.1}}
.stage{position:absolute;inset:0;perspective:1200px;perspective-origin:50% 45%}
.card{position:absolute;left:50%;top:55%;width:clamp(150px,18%,230px);padding:18px;
  border-radius:20px;background:var(--glass-bg-strong);border:1px solid var(--glass-bd-strong);
  backdrop-filter:blur(var(--glass-blur));color:var(--fg);
  transform-style:preserve-3d;
  transform:translate(-50%,-50%) translate3d(var(--x),var(--y),var(--z))
            rotateX(var(--rx)) rotateY(var(--ry)) rotate(var(--rz));
  transition:transform .6s var(--ease-soft);
  /* depth hierarchy: farther = dimmer + softer */
  opacity:calc(1 + var(--z)/900px);filter:blur(calc(var(--z)*-.004))}
.card--near{box-shadow:0 30px 60px -20px rgba(0,0,0,.55)}
.mist{position:absolute;inset:auto 0 0;height:45%;pointer-events:none;
  background:linear-gradient(to top,rgba(255,200,220,.35),transparent)}
.vignette{position:absolute;inset:0;pointer-events:none;
  background:radial-gradient(120% 90% at 50% 40%,transparent 55%,rgba(0,0,0,.55))}
@media (prefers-reduced-motion:reduce){.plate{animation:none}.card,.plate{transition:none}}
```
```js
// pointer parallax (desktop) + scroll parallax (all)
const s=document.querySelector('.scene');let tx=0,ty=0,cx=0,cy=0;
s.addEventListener('pointermove',e=>{const r=s.getBoundingClientRect();
  tx=((e.clientX-r.left)/r.width-.5)*10; ty=((e.clientY-r.top)/r.height-.5)*-8;});
s.addEventListener('pointerleave',()=>{tx=ty=0});
(function loop(){cx+=(tx-cx)*.08;cy+=(ty-cy)*.08;
  s.style.setProperty('--ry',cx+'deg');s.style.setProperty('--rx',cy+'deg');
  requestAnimationFrame(loop)})();
```
- The card carousel from the video = same cards + GSAP `ScrollTrigger` (or `motion`
  `useScroll`) mapping progress → `--x` of each card, with a wider multiplier for near
  cards (parallax). Never animate `left/top`; only `transform`/`opacity`.
- Keep `blur()` filters small (≤3px) and on ≤6 elements — compositor cost.

### Tier 2 — 2.5D depth-map displacement (the image itself gains volume)
When you want the *environment* to shift, not just the cards. One image + one grayscale
depth map (white = near), sampled per pixel in a fragment shader:
```glsl
float d = texture2D(u_depth, v_uv).r;
gl_FragColor = texture2D(u_color, v_uv + u_offset * d * u_strength); // u_strength ≈ .02–.05
```
- Depth maps: **Depth Anything V2/V3** (HF transformers, or hosted demo) → PNG (never JPEG:
  ripples), same aspect as image, smooth gradients + sharp edges. Or export Z-pass from
  the 3D tool that rendered the plate. Hand-paint for simple compositions.
- Runtime: three.js `ShaderMaterial` on a fullscreen plane, or Pixi `DisplacementFilter`,
  or a 40-line raw-WebGL. Mouse → `u_offset` with exponential smoothing (`cur+=(t-cur)*.08`).
  Gyro on mobile, calibrated against the resting pose. Fallback to Tier 1 when no WebGL.
- Cards stay DOM (Tier 1) layered on top of the canvas — text stays crisp/selectable.

### Tier 3 — Real 3D (R3F / Spline) — only when the product is 3D
Use when there is an actual object to orbit (device, packaging, configurator). Spline
for designer-authored scenes exported fast; React Three Fiber (+drei) for full control.
Cost: +~150 KB (three) +50 KB (R3F), GPU on mobile, long LCP. Lazy-load below the fold or
after `requestIdleCallback`, show a poster first. Not needed for the reference look.

## 3. Producing the backplate (AI imagery)
Prompt skeleton (Midjourney/Imagen/Flux):
`[subject: hidden forest archway opening onto pastel sunset sky] , wildflowers in blurred
foreground, volumetric haze, golden-hour rim light, cinematic, shallow depth of field,
35mm, soft pastel grading, empty center for UI, no text --ar 16:9 --style raw`
- Ask for **negative space where type/cards go** ("empty center", "clear sky upper half").
- Generate 2–3 variants, upscale 2×, export `.webp` q80 ~1920w (+ `srcset` 1280/2560).
- Low internal contrast + one warm light source → glass cards and light serif type read.
- Keep one palette across sections (plates should feel like one world), then pull the
  accent color from the plate into the tokens (e.g. `--accent` from the sunset).

## 4. Guardrails (premium-lp rules still apply)
- Motion: `--ease`/`--ease-soft` only, drifts 20–30 s, pointer parallax ≤10°, no bounce.
- `prefers-reduced-motion`: stop drift/parallax, keep the static composition.
- Mobile: stack cards 2-up, kill pointer parallax, reduce `backdrop-filter` blur to 10px,
  serve a 1280w plate. Test at 390px and 1280px with `agent-browser screenshot`.
- LCP: plate/poster `fetchpriority="high"`, `decoding="async"` on everything else.
- Contrast: check headline vs plate (≥4.5:1 — add `.vignette` or a text-shadow scrim).
- Do not put the immersive scene in every section — hero (+ optionally one showcase).
  The rest of the LP stays flat and quiet; that contrast is what makes it feel expensive.

## Sources
- Sygnal KB — 2.5D image parallax & depth maps: https://www.sygnal.com/kb/image-25d-parallax-effects
- 2.5D Depth Studio (layer vs mesh displacement): https://github.com/nermadie/2.5D_Depth_Studio
- CSS parallax patterns (perspective layers, scroll-timeline hero): https://codefronts.com/motion/css-parallax-designs/
- GSAP ScrollTrigger examples: https://animation-addons.com/blog/gsap-scrolltrigger-examples/
- Codrops infinite scroll w/ GSAP + Lenis (2026): https://tympanus.net/codrops/2026/05/28/the-never-ending-story-building-a-seamless-infinite-scroll-experience-with-gsap-lenis/
- R3F vs three.js 2026: https://graffersid.com/react-three-fiber-vs-three-js/ · Spline vs R3F: https://medium.com/@akbar123jason/simplifying-3d-integration-in-react-spline-vs-react-three-fiber-3f5e1a9e39d3
- Awwwards immersive trend: https://digitalstrategyforce.com/journal/why-are-immersive-experiences-dominating-the-2026-awwwards/ · inspiration: https://www.awwwards.com/websites/parallax/
- Midjourney fantasy environment prompts: https://aituts.com/midjourney-fantasy/
