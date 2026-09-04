# Recreate this site as a single HTML file: Stride

You are an expert creative front-end developer. Produce a **single self-contained `index.html`**
that reproduces the project below **exactly** — same layout, sections, visuals, motion, and
interaction. Pure HTML/CSS/JS in one file: **no build step, no framework, no bundler**. Use ES
modules with CDN importmaps for the one library used (Three.js **r0.143.0** from unpkg). Hardcode
every value given here as a fixed constant. Rebuild each component described below as one section of
the single file. Faithfulness beats brevity — copy the GLSL shaders **verbatim** and bake in every
number.

## What it is

Stride is a dark, deep-blue **fintech "modern banking" landing page**. It opens on a full-bleed
WebGL **plasma burst** — a spherical spray of glowing violet/indigo filaments and white sparks with
a hot core, bloom-lit on a blue "mesh gradient" backdrop — with the marketing hero (display heading,
stats row, insight block + CTAs) laid over it. As you scroll, the whole hero **shrinks into a
rounded inset card** while the burst spins faster and zooms in. Below: two logo rows that marquee in
opposite directions (scroll-linked), a centred About statement with inline icon chips, a bento
**stats** grid, a hover-reveal **showcase** of four columns, a pinned 3D **portfolio** card stack on
a cylinder, then two **layered pinned reveals** — a chrome **chain** model pinned while a Product
bento scrolls up over it, and a contact form pinned while a blue-mesh footer (with a chrome **heart**
model) scrolls up over it. Motion is smooth and spring-like throughout; a white **preloader** with a
counter and a growing gradient bar plays first, then slides up to reveal the hero. Palette: near-black
`#04070f`, electric blues (`#1c3ee6`, `#2f7cf6`, `#2e9bff`), a lime accent (`#b6f13a`), deep plum
(`#241c3a`), and white.

## Page shell & libraries

**Libraries.** Only Three.js (r0.143.0 — the scenes rely on `WebGL1Renderer`, `outputEncoding`,
`sRGBEncoding`, which are r0.143-era APIs). Add this importmap in `<head>`:

```html
<script type="importmap">
{
  "imports": {
    "three": "https://unpkg.com/three@0.143.0/build/three.module.js",
    "three/examples/jsm/": "https://unpkg.com/three@0.143.0/examples/jsm/"
  }
}
</script>
```

You will import from `three`, plus these addons: `EffectComposer`, `RenderPass`, `UnrealBloomPass`,
`ShaderPass`, `GammaCorrectionShader` (postprocessing), `GLTFLoader`, `DRACOLoader`,
`RoomEnvironment`. For DRACO decoding set `draco.setDecoderPath("https://www.gstatic.com/draco/versioned/decoders/1.5.6/")`.

**Font.** Mulish (weights 300 / 400 / 500). Load from Google Fonts (a permitted exception: one
`<link>` — or, if you must stay fully offline, use a system sans stack). Apply site-wide:
`font-family: "Mulish", ui-sans-serif, system-ui, sans-serif;`

**CSS reset & root scaling.** Minimal reset (`*{margin:0;box-sizing:border-box}`). The original scales
the root font-size with the viewport so its rem-based layout stays proportional — reproduce it:

```css
html { font-size: 16px; }
@media (max-width: 1440px) { html { font-size: 1.111111vw; } }  /* 16*100/1440 */
@media (max-width: 1024px) { html { font-size: 1.5625vw; } }    /* 16*100/1024 */
@media (max-width: 640px)  { html { font-size: clamp(12px, 1.5625vw, 16px); } }
```

Above 1440px keep 16px (do not scale up further — approximates the original AdaptiveGrid). Because
of this, most sizes below are given in `rem`/`px` exactly as the source; treat `text-4xl`=2.25rem,
`text-5xl`=3rem, `text-6xl`=3.75rem, `text-7xl`=4.5rem, `text-8xl`=6rem, `text-2xl`=1.5rem,
`text-3xl`=1.875rem, `text-lg`=1.125rem, `text-base`=1rem, `text-sm`=0.875rem, `text-xs`=0.75rem
(standard Tailwind scale). `page-gutter` = **120px** (wide sections inset 120px left/right on `lg`).

**Design tokens (CSS custom properties on `:root`).** Bake these in exactly:

```css
--hero-base:#04070f; --hero-stage:#1246e2; --hero-glow:#2ad4ff; --hero-page:#ffffff;
--logo:#c6ccd6; --accent-blue:#2e9bff; --accent-lime:#b6f13a; --muted:#9aa1ad;
--card-blue:#2f7cf6; --card-gray:#ededf0; --card-dark:#0a0c12;
--plum:#241c3a; --lilac:#d7d1f2; --paper:#f5f4f8;
--footer:#0a1533; --footer-fg:#f2f4f1; --footer-muted:#aeb9d8;
--preloader-from:#d7e0ff; --preloader-to:#1b34e8;
```

`bg-hero-page` = white `#ffffff` (the page background). `text-black` = `#000`. Body background is
white. Theme-color (mobile address bar) = `#04070f`.

**Scroll model.** The whole page scrolls natively (tall document). The original uses **Lenis smooth
scroll** (`lerp: 0.08`) so the page glides — reproduce a smooth-scroll feel: implement a lightweight
scroll smoother (a rAF loop that lerps a `scrollY` proxy) **or** rely on native scroll with
`scroll-behavior` untouched. What matters is that all the scroll-driven effects below read
`window.scrollY` / `getBoundingClientRect().top` every animation frame. Run **one shared rAF ticker**
that every scroll-driven component subscribes to (the original centralises this). Respect
`prefers-reduced-motion`: jump animations to their end state and render WebGL scenes as single static
frames.

**Motion primitives (translate the spring engine to vanilla).** The original drives every reveal by
advancing a value `p` from 0→1 over a fixed duration from the shared ticker, then deriving each
element's opacity/transform/blur from `p` — do exactly this in vanilla JS (no CSS keyframes needed;
you set inline styles each frame). Two reusable helpers:

- **Per-letter / per-item reveal cascade.** `DURATION = 1400ms`, `SPREAD = 0.55`,
  `easeOutQuart(t) = 1 - (1-t)^4`. For item `i` of `total`, its start is
  `s = total>1 ? (i/(total-1))*SPREAD : 0`, span `= 1 - SPREAD`, and its local progress is
  `localProg(v,i) = easeOutQuart(clamp01((v - s) / span))`. Headings split into letters (words kept
  unbreakable via `inline-block` word wrappers, letters `inline-block` inside): each letter animates
  `opacity = alpha*localProg`, `transform: translateY(RISE*(1-localProg))` with `RISE = 16px`,
  `filter: blur(BLUR*(1-localProg))` with `BLUR = 8px`. Headings that **contain a digit** render
  static (no per-letter animation) — a deliberate rule.
- **Trigger:** headings/cards start the cascade when they scroll into view
  (IntersectionObserver, `rootMargin: "0px 0px -15% 0px"`). The hero's non-heading elements instead
  start when the preloader finishes.

## Layout & sections (in order)

The DOM order is: **`SiteNav`** (fixed, once), then `<main id="top">` containing → Hero → LogoMarquee
→ About → Stats → Showcase → Works → `<div style="position:relative">`(Chain + Product) →
`<div style="position:relative">`(ContactForm + SiteFooter). Plus the **Preloader** overlay.

### SiteNav (fixed header, rendered once)

`position: fixed; inset-x: 16px; top: 16px` (`md`: 24px); `z-index: 50`; flex row, space-between,
white text. Left group: a **brand pill** and a **links pill**, both a shared shell
`display:flex; height:44px; align-items:center; border-radius:9999px; border:1px solid rgba(255,255,255,.1); background:rgba(0,0,0,.3); backdrop-filter:blur(12px);`.
Brand pill: `gap:8px; padding-inline:20px; font-weight:500` — a bolt icon + text **"Stride"**. Links
pill (hidden below `md`): `padding-inline:8px; display:flex` — anchors "Services", "How it works",
"About us", "Testimonials", each `border-radius:9999px; padding:8px 16px; font-size:.875rem;
color:rgba(255,255,255,.8)` (hover → white). Right: a white pill button, `height:44px;
padding-inline:24px; background:#fff; color:var(--plum); font-size:.875rem; font-weight:500;
border-radius:9999px` — text **"Request a demo"**. Bolt icon SVG (`viewBox="0 0 24 24"`,
`fill=currentColor`): `<path d="M13 2 4.5 13.5H11l-1 8.5L19.5 10H13z"/>`.

### Hero

`<section>` `position:relative; height:100lvh; background:#fff`. Inside, an **inset-clip wrapper**
`position:absolute; inset:0; overflow:hidden` whose `clip-path` is scroll-driven:
`inset(${p*24}px round ${p*24}px)` — as scroll progress `p` (0→1) grows the card insets 24px from
each edge with a 24px corner radius, revealing white behind it. Progress:
`p = clamp01(-sectionTop / (innerHeight * 0.6))` (0.6 viewports of scroll to complete). Ease `p`
toward its target (spring-like, ~`tension 420, friction 44` feel — a quick critically-damped lerp,
e.g. `p += (target-p)*0.18` per frame is close enough).

Inside the clip wrapper, in order:
1. **The two WebGL canvases** (see "WebGL scenes"): a gradient-background canvas
   (`position:absolute; inset:0`), and over it the plasma-burst canvas
   (`position:absolute; inset:0; mix-blend-mode:lighten`). The burst renders on pure black and
   composites over the blue gradient via `mix-blend:lighten`.
2. **Marketing overlay** `position:absolute; inset:0; z-index:10; display:flex; flex-direction:column;
   padding:20px 24px 24px` (`md`: `40px`, `lg`: `48px` sides/bottom); white text. Its `transform:
   scale(1 - p*0.1)` so the UI recedes as the card shrinks (transform-origin center).
   - **Display heading (`<h1>`)** — `margin-top:6rem` (clears the fixed header),
     `font-size:2.25rem`→`sm:3.75rem`→`lg:6rem`, `font-weight:300; line-height:0.95;
     letter-spacing:-0.02em`. Two lines: **"Turn balance into"** / **"momentum"**. Per-letter intro
     (see below), with a per-line alpha ramp reproducing an old inverted gradient: even lines fade
     `alpha = 1 - pos*0.6` (white→muted), odd lines `alpha = 0.4 + pos*0.6` (muted→white), where
     `pos` is the letter's 0..1 position on its line.
   - **Bottom band** `margin-top:auto; display:flex; flex-direction:column; gap:2.5rem`
     (`lg`: row, `align-items:flex-end; justify-content:space-between`):
     - **Stats `<ul>`** (left) `display:flex; gap:1.5rem` (`sm`: 2.5rem). Three `<li>`, each
       `border-left:1px solid #fff; padding-left:1.25rem`. Value `display:block; font-size:3rem`
       (`sm`:4.5rem)`; font-weight:300; line-height:1`. Label `margin-top:.75rem; font-size:.875rem`.
       Stats: **4.9 / User Reviews**, **30k / Active clients**, **15k / Good Feedback**.
     - **Insight block** (right) `max-width:32rem`:
       - `<p>` `font-size:1.5rem; font-weight:500; line-height:1.375` — **"Get the insight and
         control to act with confidence, exactly when it counts."**
       - `<p>` `margin-top:1rem; font-size:1rem; line-height:1.625; color:rgba(255,255,255,.55)`;
         **hidden below `sm`** — **"Watch your money work as hard as you do. Automated saving, real
         returns, and a clear view of where every dollar goes — no jargon, no guesswork."**
       - CTA row `margin-top:4rem; display:flex; align-items:center; gap:1.5rem`: a **white pill
         button** (`background:#fff; color:#000; border-radius:9999px; padding:12px 24px;
         font-size:.875rem; font-weight:500`) text **"Request a demo"**, and an underlined link
         (`color:#fff; text-decoration:underline; text-underline-offset:4px`) text **"Learn more"**.

**Hero per-letter title intro** — `DURATION = 1600ms`, `SPREAD = 0.55`, `RISE = 16px`, `BLUR = 8px`,
`easeOutQuart`. Letters are staggered across the whole two-line heading by a running global index.
Each letter: `opacity = alpha*localProg`, `translateY(RISE*(1-localProg))`, `blur(BLUR*(1-localProg))`.
**Plays only after the preloader finishes** (reduced-motion → jump to end).

**Hero non-heading reveal** — the 3 stats, insight title, insight body, and CTA row (6 items) run the
reveal cascade with `RISE = 20px`, `BLUR = 10px`, starting when the preloader finishes.

### LogoMarquee

`<section>` `background:#fff; padding-block:3rem`. A `flex-direction:column; gap:2.5rem` holding two
rows. Each **row** is `display:flex; overflow:hidden`; its inner track is `display:flex; width:max-content;
align-items:center; color:var(--logo)` and holds the logo set repeated **6 times** (`COPIES = 6`).
Each logo item: `display:flex; align-items:center; gap:.75rem; padding-right:4rem` — a 36×36px SVG
mark (`fill=currentColor`) + a name `font-size:1.875rem; font-weight:600; letter-spacing:-0.02em;
white-space:nowrap`. Names: **Northwind, Ledgerly, Vaulted, Paycore, Finova, Quanta, Settle, Corvus**.

Five mark shapes cycled by index `i % 5` (`viewBox="0 0 24 24"`):
`<circle cx="12" cy="12" r="9"/>`,
`<rect x="4" y="4" width="16" height="16" rx="5"/>`,
`<path d="M12 2.5 21 20.5 3 20.5Z"/>`,
`<path d="M12 3a9 9 0 1 0 9 9h-9Z"/>`,
`<path d="M12 2l2.6 6.4L21 9l-5 4.2L17.8 20 12 16.3 6.2 20 8 13.2 3 9l6.4-.6z"/>`.

**Scroll-linked motion** (not autoplay). `ONE_SET = 100/6` (percent width of one set). Each frame set
`s = window.scrollY * speed` and ease it (spring feel `tension 90, friction 34` — a gentle lerp).
Row 1 direction **left**, `speed = 0.005`; Row 2 direction **right**, `speed = 0.0038`. Transform:
`m = ((s % ONE_SET) + ONE_SET) % ONE_SET; off = direction==="left" ? -m : -(ONE_SET - m);
track.style.transform = translate3d(off%,0,0)` — stays within `[-ONE_SET, 0]` so it's seamless.

### About

`<section>` `background:#fff; padding:4rem 1.5rem 8rem; text-align:center`. 
- **Eyebrow `<p>`** `margin-bottom:1.5rem; font-size:.75rem; font-weight:600; text-transform:uppercase;
  letter-spacing:0.35em; color:var(--muted)` — a small dot (`inline-block; height:6px; width:6px;
  border-radius:9999px; background:var(--plum); margin-right:.5rem; vertical-align:middle;
  transform:translateY(-0.15em)`) + text **"About us"**. Reveals via a simple fade+rise (opacity
  0→1, y 12→0) on scroll-in.
- **Statement `<h2>`** `margin:0 auto; max-width:56rem; font-size:2rem`→`sm:2.5rem`→`lg:4rem`;
  `font-weight:300; line-height:0.95; letter-spacing:-0.02em; color:#000`. Assembled by hand as one
  staggered reveal timeline over: lead letters · chip1 · muted letters · chip2.
  - Lead run (black): **"A fintech platform built to move money"**
  - **Chip 1** (inline round chip): `display:inline-flex; height:0.82em; width:0.82em;
    margin-inline:0.15em; transform:translateY(0.08em); align-items:center; justify-content:center;
    border-radius:9999px; color:#fff; background:var(--accent-blue)` containing a bar-chart icon
    (`viewBox="0 0 24 24"`, `height/width:0.5em`): `<rect x="4" y="12" width="4" height="8" rx="1"/>
    <rect x="10" y="7" width="4" height="13" rx="1"/> <rect x="16" y="3" width="4" height="17" rx="1"/>`.
  - Muted run (`color:var(--muted)`): **"smarter and settle payments faster"**
  - **Chip 2**: same chip shell but `background:var(--accent-lime); color:#000`, containing a bolt
    icon (`height/width:0.55em`): `<path d="M13 2 4 14h6l-1 8 9-12h-6z"/>`.
  - Timeline: letters animate `opacity=localProg`, `translateY(16px*(1-localProg))`,
    `blur(8px*(1-localProg))`. Chips animate `opacity=localProg`,
    `scale(0.3 + 0.7*localProg)` (`CHIP_MIN_SCALE = 0.3`), `blur(8px*(1-localProg))`. Total items =
    (lead letters) + 1 + (muted letters) + 1, spaces excluded from letter counts.

### Stats

`<section>` `background:#fff; padding:0 1.5rem 8rem` (`md`: 3rem sides, `lg`: **120px** sides). A grid
`grid-template-columns:1fr; gap:1.5rem` (`lg`: 3 columns). Cards do a staggered reveal (4 items,
`CARD_RISE = 24px`, `CARD_BLUR = 12px`): `opacity=localProg`, `translateY(24*(1-localProg))`,
`blur(12*(1-localProg))` on scroll-in. Number style (shared): `font-weight:300; line-height:1;
letter-spacing:-0.02em; font-size:2rem`→`sm:2.5rem`→`lg:4rem`.

1. **Collaboration card (blue)** `position:relative; min-height:32rem; display:flex;
   flex-direction:column; justify-content:flex-end; overflow:hidden; border-radius:1.5rem;
   background:var(--card-blue); padding:1.75rem; color:#000`. A full-cover `<img>` (people photo,
   `object-fit:cover`), a gradient scrim `position:absolute; inset:0;
   background:linear-gradient(to bottom, rgba(255,255,255,.2), rgba(0,0,0,.25))`. Top-left brand
   **"LEDGERLY"** (`position:absolute; left:1.75rem; top:1.75rem; font-size:1.5rem;
   font-weight:800; font-style:italic; letter-spacing:-0.02em`). Top-right a 44×44px white rounded
   square (`border-radius:.75rem; background:#fff; color:var(--card-blue)`) with the bar-chart icon
   (20×20). Bottom: a white inner card `border-radius:1rem; background:#fff; padding:1.5rem;
   color:#000` — number **"120+"** then `<p>` `margin-top:.75rem; max-width:20rem; font-size:1rem;
   line-height:1.625; color:rgba(0,0,0,.6)` **"Partnered with leading banks and payment networks."**
2. **Commitment card (grey)** `min-height:32rem; display:flex; flex-direction:column;
   border-radius:1.5rem; background:var(--card-gray); padding:2rem; color:#000`. Top `<p>`
   `font-size:1rem; color:rgba(0,0,0,.55)` **"Commitment to uptime"**; number **"99.99%"**
   (`margin-top:1rem`). Pushed to base (`margin-top:auto`): an avatar stack (4 avatars, each 44×44px
   round, `border:2px solid var(--card-gray); margin-left:-0.75rem; first:margin-left:0;
   overflow:hidden`) then a `<blockquote>` `max-width:28rem; font-size:1.5rem; line-height:1.375;
   letter-spacing:-0.02em; color:rgba(0,0,0,.8)` with curly quotes: **"Their payment rails completely
   reshaped how we move money. It's fast, reliable, and secure."**
3. **Right column** (`display:flex; flex-direction:column; gap:1.5rem`):
   - **Data card (blue)** `flex:1; border-radius:1.5rem; background:var(--card-blue); padding:2rem;
     color:#fff`. Label `font-size:.875rem; color:rgba(255,255,255,.7)` **"Data points"**; number
     **"520k+"**; base `<p>` `margin-top:auto; max-width:20rem; color:rgba(255,255,255,.8)`
     **"Analyzed every day to power smarter financial decisions."**
   - **Reach card (dark)** `display:flex; align-items:center; justify-content:space-between;
     border-radius:1.5rem; background:var(--card-dark); padding:2.5rem 2rem; color:#fff`. Left label
     `font-size:1.125rem; color:rgba(255,255,255,.55)` **"Countries"**; right the number **"60+"**.

Avatars: images `1.png`..`4.png` (see Assets). People photo: `people.png`.

### Showcase

`<section>` `position:relative; overflow:hidden; border-top:1px solid rgba(36,28,58,.1);
border-bottom:1px solid rgba(36,28,58,.1); background:#fff; color:#000`.
- **Heading + CTA** block: on mobile/tablet in normal flow above the cards (`padding:3.5rem 1.5rem
  2.5rem`, `md`:2.5rem sides); on `lg` `position:absolute; left:50%; right:0; top:3.5rem; z-index:20;
  padding-inline:3rem; pointer-events:none` (so columns stay hoverable). `<h2>` (uses the per-letter
  reveal) `font-size:2rem`→`sm:2.5rem`→`lg:3rem`; `font-weight:300; line-height:0.95;
  letter-spacing:-0.02em` — **"Thoughtful engineering behind every detail"**. Below, an **outline
  button** `pointer-events:auto; margin-top:2rem; border:1px solid rgba(0,0,0,.3); border-radius:
  9999px; padding:12px 24px; font-size:.875rem; font-weight:500` (hover: black bg, white text) —
  **"Explore the platform"**.
- **Columns grid**: mobile/tablet a 2-column grid `gap:1rem; padding:0 1.5rem 1.5rem`; `lg` a
  4-column, full-height, edge-to-edge grid with `divide-x` thin lines `rgba(36,28,58,.1)`, `gap:0`.
  Four columns. Each `<article>` `position:relative; min-height:38vh` (`lg`:85vh); `overflow:hidden;
  border-radius:1rem` (`lg`: 0).
  - On mobile/tablet the photo is always shown (`object-fit:cover`) with a fixed dark bottom scrim
    (`height:50%; background:linear-gradient(to top, rgba(0,0,0,.7), rgba(0,0,0,.3), transparent)`).
  - On `lg` the photo is hidden by default and **revealed on hover** of the whole column: animate a
    photo layer's `clip-path` from `inset(100% 0% 0% 0%)` → `inset(0% 0% 0% 0%)` (bottom-to-top),
    and a white bottom scrim (`height:50%; background:linear-gradient(to top, #fff, rgba(255,255,255,.8),
    transparent); transform-origin:bottom`) scaling `scaleY(0)`→`scaleY(1)`. Both eased spring-like
    (`tension 170, friction 26` → e.g. a ~250ms ease). The column is the hover target.
  - Label `position:absolute; left:1.25rem; bottom:1.5rem` (`lg`:1.5rem/2rem), two `<span>` lines
    `font-size:1.5rem`(`lg`:2.25rem)`; font-weight:400; line-height:1.05; letter-spacing:-0.02em;
    color:#fff` (`lg`: `#000`, since the hover-revealed white scrim sits behind it).
  - Items: **"Our" / "Approach"** (`approach.png`), **"Our" / "Technology"** (`technology.jpg`),
    **"Our" / "Security"** (`security.jpg`), **"Our" / "Team"** (`team.jpg`).

### Works (pinned 3D portfolio card stack)

`<section>` `position:relative; background:var(--card-dark); color:#fff;
height:${100 + (count-1)*60}vh` with `count = 6` → **`height:400vh`** (`SCROLL_PER_CARD = 60vh`).
Inside a **sticky** stage `position:sticky; top:0; height:100lvh; overflow:hidden`.

Scroll progress `p = clamp01(-sectionTop / (sectionHeight - innerHeight))`. Map to a float card index
that overshoots each end: `OVERSCAN = 0.2`, `target = p*(count-1 + OVERSCAN*2) - OVERSCAN`. Low-pass:
`current += (target - current)*0.14` each frame; drive everything off `f = current`.

Layers (all read `f` per frame):
1. **Blurred backdrop** `position:absolute; inset:0`: for each card `i`, a full-cover blurred photo
   (`filter:blur(40px)` / `blur-2xl`, `object-fit:cover`) with
   `opacity = max(0, 1 - |i - f|)`, `transform: translateY((i-f)*22%) scale(1.6)`. Over them a dim
   layer `background:rgba(10,12,18,.55)`.
2. **Heading `<h2>`** `position:absolute; inset-x:0; top:6rem; z-index:30; text-align:center;
   font-size:2rem`→`sm:2.5rem`→`lg:4rem`; `font-weight:300; line-height:0.95; letter-spacing:-0.02em`
   — **"Our Portfolio"** (per-letter reveal).
3. **3D stage** `position:absolute; inset:0; perspective:1900px; transform-style:preserve-3d`. Cards
   on a **vertical cylinder**, `RADIUS = 1350`, `STEP = 27` (deg between adjacent cards). Each card
   `position:absolute; left:50%; top:50%; aspect-ratio:16/10; width:90vw; max-width:46rem;
   overflow:hidden; border-radius:1.5rem; background:var(--card-dark); box-shadow: large;
   backface-visibility:hidden`. Per frame, with `rel = i - f`, `rad = rel*STEP*π/180`,
   `y = RADIUS*sin(rad)`, `z = RADIUS*cos(rad) - RADIUS`, `d = |i - f|`:
   `transform = translate3d(0, ${y}px, ${z}px) rotateX(${rel*STEP}deg) translate(-50%,-50%)`,
   `opacity = d > 3.6 ? 0 : max(0, 1 - d*0.24)`. Inside each: the portfolio photo
   (`object-fit:cover`) + a black scrim `position:absolute; inset:0; background:#000` with
   `opacity = min(0.35, |i - f|*0.22)` (focused card stays clear, neighbours dim with depth).
4. **Metadata row** `position:absolute; inset-x:1rem; top:50%; transform:translateY(-50%); z-index:30;
   display:grid; grid-template-columns:auto 1fr auto; align-items:center; gap:1rem; font-size:.875rem`.
   Left: `<span>` index/count `tabular-nums; color:rgba(255,255,255,.5)` showing `NN / 06` where
   `NN` updates to `round(current)+1` (pad to 2 digits). Centre: card **name**
   `font-size:1.125rem`(`sm`:1.5rem)`; color:#fff; text-align:center; truncate` updating to the
   focused card's name. Right: an outline pill button `border:1px solid rgba(255,255,255,.25);
   border-radius:9999px; padding:10px 20px; color:rgba(255,255,255,.8)` — **"View project"**.

Items (name, image): **Northwind** `1.jpg`, **Ledgerly** `2.jpg`, **Vaulted** `3.jpg`,
**Paycore** `4.jpg`, **Finova** `5.jpg`, **Corvus** `6.jpg`.

### Chain + Product (layered pinned reveal #1)

Wrap both in `<div style="position:relative">`. The **Chain** pins (`position:sticky; top:0;
z-index:10; height:100lvh; overflow:hidden`) while the **Product** (`position:relative; z-index:20`)
scrolls up over it.

**Chain** — two stacked canvases `position:absolute; inset:0`: a gradient-background canvas
(`BASE = #1c3ee6`, `LIGHT = #eef3ff`, `SEED = 3.7`) and over it a **chrome chain model** canvas
(transparent, GLB `chain.glb`, chrome material, `scrollSpin: true`, `spinAccel: 0.00004`,
`maxSpin: 0.045`). Marketing overlay `position:absolute; inset:0; z-index:10; display:flex;
flex-direction:column; padding:20px 24px 24px` (`md`:40px, `lg`:48px), white text:
- **`<h2>`** `margin-top:6rem; max-width:15ch; font-size:3rem`→`sm:3.75rem`→`lg:6rem`; `font-weight:300;
  line-height:0.95; letter-spacing:-0.02em; color:#fff` — **"Financial momentum"** (per-letter
  reveal with a per-letter alpha ramp `alpha(f) = 1 - f*0.65`).
- Bottom row `margin-top:auto; display:flex; align-items:flex-end; justify-content:space-between;
  gap:2rem`: left `<p>` `max-width:28rem; font-size:1rem; line-height:1.625; color:#fff` —
  **"Watch your money work as hard as you do. Automated saving, real returns, and a clear view of
  where every dollar goes — no jargon, no guesswork."**; right (only `lg`+) `<p>` `max-width:36rem;
  text-align:right; font-size:6rem; font-weight:300; line-height:0.95; letter-spacing:-0.02em;
  color:rgba(255,255,255,.4)` — **"when it matters most"**.

**Chain model vertical drive** (off the *wrapper's* scroll, not the pinned section): `vh =
innerHeight`, `wt = wrapper.getBoundingClientRect().top`, `pinDist = max(1, wrapper.offsetHeight - vh)`.
`target = wt > 0 ? 0.5*(1 - min(wt/vh, 1)) : 0.5 + 0.5*min(-wt/pinDist, 1)`. Low-pass:
`fall += (target - fall)*0.12`. Feed `fall` (0..1) as the model's fall progress (0=above,
0.5=centred, 1=below).

**Product** — `<section>` `position:relative; z-index:20; background:#fff; padding:6rem 1.5rem;
color:#000` (`md`:3rem sides, `lg`: **120px** sides). Centred heading block `margin-bottom:3.5rem;
display:flex; flex-direction:column; align-items:center; text-align:center`: `<h2>` (per-letter
reveal) `font-size:2rem`→`sm:2.5rem`→`lg:4rem`; `font-weight:300; line-height:0.95;
letter-spacing:-0.02em` — **"What is Northwind?"**; a **black pill button** `margin-top:2rem;
background:#000; color:#fff; border-radius:9999px; padding:12px 24px; font-size:.875rem;
font-weight:500` — **"Explore now"**. Bento grid `display:grid; gap:1.25rem` (`lg`: 4 columns), 3
cards reveal-staggered (`CARD_RISE = 24`, `CARD_BLUR = 12`):
1. **Wide photo card** (`lg:grid-column:span 2`) `position:relative; min-height:26rem; display:flex;
   flex-direction:column; justify-content:space-between; overflow:hidden; border-radius:1.5rem;
   padding:2rem; color:#000` with a full-cover `<img>` (`6th.png`, `object-fit:cover`). Over it (`position:relative`):
   `<h3>` `font-size:1.5rem; line-height:1.375; letter-spacing:-0.02em` **"Capital that compounds"**;
   `<p>` `max-width:20rem; font-size:1.125rem; color:rgba(0,0,0,.7)` **"Your balance earns
   automatically as it's routed into vetted, high-yield lending markets."**
2. **Grey card** `min-height:26rem; display:flex; flex-direction:column; justify-content:space-between;
   border-radius:1.5rem; background:var(--card-gray); padding:2rem` — `<h3>` **"Always liquid, always
   steady"**, `<p>` (`color:rgba(0,0,0,.7)`) **"Move your money the moment you need it — fully backed,
   with no lockups or waiting."**
3. **Grey card** (same style) — `<h3>` **"Fully hands-off"**, `<p>` **"Nothing to manage. Northwind
   rebalances in the background, day and night."**

### ContactForm + SiteFooter (layered pinned reveal #2)

Wrap both in `<div style="position:relative">`. The **ContactForm** pins
(`position:sticky; top:0; z-index:10; height:100lvh`) while the **SiteFooter**
(`position:relative; z-index:20`) scrolls up over it.

**ContactForm** — `<section id="contact">` `display:flex; flex-direction:column; justify-content:center;
background:#fff; padding:6rem 1.5rem; color:#000` (`md`:3rem, `lg`:120px sides). `<h2>` (per-letter
reveal) `margin:0 auto 4rem; max-width:42rem; text-align:center; font-size:1.5rem; line-height:1.375;
letter-spacing:-0.02em` — **"Stride is always looking to connect with sharp, curious people. Feel
free to drop us a line."** `<form>` `display:flex; flex-direction:column; gap:2.5rem` (no live
backend — on submit `preventDefault` and show a status line). Row 1 `display:grid; gap:2.5rem`
(`md`: 2 columns): **Name** (text) and **Phone number** (tel). Below: **Email** (email). Each field:
a wrapper `position:relative; border-bottom:1px solid rgba(0,0,0,.25); padding-bottom:.75rem`
(`focus-within` → border `#000`), an `<input>` `width:100%; background:transparent; padding-right:
1.5rem; font-size:1.125rem; color:#000; outline:none` with `placeholder` uppercased, letter-spaced,
`color:rgba(0,0,0,.4)` (a `sr-only` `<label>`). A trailing status **dot** `position:absolute;
right:0; top:50%; transform:translateY(-50%); height:10px; width:10px; border-radius:9999px` —
`rgba(0,0,0,.25)` empty, `#000` when the field has a value. Submit row `margin-top:2.5rem;
display:flex; flex-direction:column; align-items:center; gap:1rem`: a **black pill button** — **"Send"**
— and a status `<p>` (`min-height:1.25rem; font-size:.875rem; color:rgba(0,0,0,.6)`) that shows
**"Thanks — we'll be in touch shortly."** after submit.

**SiteFooter** — `<footer>` `position:relative; z-index:20; display:flex; flex-direction:column;
min-height:100lvh; overflow:hidden; background:var(--footer); color:var(--footer-fg);
padding:5rem 1.5rem 1.5rem` (`md`:2.5rem, `lg`:3rem sides/bottom). Behind the content, a **FooterScene**
`position:absolute; inset:0; pointer-events:none`: two canvases — a gradient background
(`BASE = #1c3ee6`, `LIGHT = #eef3ff`, `SEED = 7.2`) and a **chrome heart model** (`heart.glb`, chrome
material, `spinDirection: -1` — spins opposite the chain). The heart flies in with parallax: with
`absTop` = the footer's absolute document top, `y = scrollY`, `enter = clamp01((y - (absTop - vh)) /
vh)`, feed `fall = 0.5*enter` (0=above the frame → 0.5=centred once fully in view).
Content (`position:relative; z-index:10; flex:1; display:flex; flex-direction:column`):
- Top, centred: `<h2>` (per-letter reveal) `margin:0 auto; max-width:14ch; font-size:2rem`→
  `sm:2.5rem`→`lg:4rem (max-width:none)`; `font-weight:300; line-height:1.05; letter-spacing:-0.02em`
  — **"Have questions? Let's talk."**; an **outline pill** `<a href="#contact">` `margin-top:2rem;
  border:1px solid rgba(242,244,241,.4); border-radius:9999px; padding:12px 24px; font-size:.875rem;
  font-weight:500` (hover: bg `--footer-fg`, text `--footer`) — **"Fill in the form"**.
- `flex:1` spacer (the heart settles in this open centre).
- **Footer nav** `<nav>` pushed to base, `margin-top:4rem; border-top:1px solid rgba(242,244,241,.15);
  padding-top:3rem` with negative side margins breaking the divider to the screen edges then re-inset;
  `display:flex; flex-direction:column; gap:2.5rem` (`md`: row, `justify-content:space-between`).
  Column titles: `margin-bottom:1rem; font-size:.75rem; font-weight:500; text-transform:uppercase;
  letter-spacing:0.05em; color:var(--footer-muted)`. Column links: `font-size:.875rem;
  color:rgba(242,244,241,.8)` (hover → full). Left group (`display:flex; gap:3rem`, `sm`:5rem):
  - **"Links"** → **"Back to top"** (`href="#top"`).
  - **"Sitemap"** → **Product, Pricing, Security, About, Careers** (all `href="#"`).
  Right group (`display:flex; gap:3rem`, `sm`:5rem):
  - **"Contact"** → an `<address>` (not italic): **"20 Harbour Street, Suite 300, London EC2 1AB"**,
    a tel link **"+44 (0)20 7946 0182"**, a mailto link **"hello@stride.finance"**.
  - **"Follow us"** → **X (Twitter)** (`https://x.com`), **LinkedIn** (`https://linkedin.com`),
    **Instagram** (`https://instagram.com`) — all `target="_blank" rel="noopener"`.

## The WebGL / canvas scenes

There are **three** scene factories, reused across five canvases: the **gradient background** (hero,
chain, footer), the **plasma burst** (hero), and the **chrome model** (chain, footer heart). Each
owns its own renderer/loop; size to its parent element's rect and observe resize. Gate rAF loops by
IntersectionObserver (only render while on/near screen). Reduced-motion → render a single static
frame.

### Scene A — Gradient background (fullscreen mesh-gradient fragment shader)

`WebGL1Renderer`, `setPixelRatio(min(dpr,2))`. A `THREE.Camera` (default) and a full-screen
`PlaneGeometry(2,2)` with a `ShaderMaterial` (`depthTest:false, depthWrite:false`). Uniforms:
`uResolution = vec2(w*dpr, h*dpr)`, `uBase` (hex→vec3), `uLight` (hex→vec3), `uSeed` (float, default 0;
hero uses default, chain `3.7`, footer `7.2`). Renders once and on resize/colour change (no loop).

Vertex shader:

```glsl
varying vec2 vUv;
void main(){ vUv = uv; gl_Position = vec4(position.xy, 0.0, 1.0); }
```

Fragment shader (VERBATIM):

```glsl
precision highp float;
varying vec2 vUv;
uniform vec2  uResolution;
uniform vec3  uBase;
uniform vec3  uLight;
uniform float uSeed;

// Soft elliptical gaussian blob — the building block of the smooth mesh gradient.
float blob(vec2 uv, vec2 c, vec2 r){ vec2 d = (uv - c) / r; return exp(-dot(d, d)); }

void main(){
  vec2 uv = vUv;                       // 0..1, (0,0) bottom-left
  vec3 navy   = vec3(0.015, 0.028, 0.085); // deep blue-black (keeps a blue hue, not grey)
  vec3 ltblue = vec3(0.42, 0.60, 1.0);

  // Base: vivid blue, only a gentle vertical falloff (stays saturated).
  vec3 col = mix(uBase * 0.88, uBase * 1.08, smoothstep(0.0, 1.0, uv.y));

  // Per-blob offset — same style, different arrangement when uSeed != 0.
  vec2 o1 = 0.22 * vec2(sin(uSeed * 1.3), cos(uSeed * 1.9));
  vec2 o2 = 0.22 * vec2(sin(uSeed * 2.1 + 1.7), cos(uSeed * 1.1 + 0.6));
  vec2 o3 = 0.22 * vec2(sin(uSeed * 0.7 + 3.1), cos(uSeed * 2.4 + 2.2));
  vec2 o4 = 0.22 * vec2(sin(uSeed * 1.7 + 4.2), cos(uSeed * 0.9 + 5.0));

  // Smooth dark blobs — soft-edged, overlapping into one blue-black mass. No turbulence.
  float dark = 0.0;
  dark += blob(uv, vec2(0.06, 0.86) + o1, vec2(0.30, 0.34)) * 0.85;
  dark += blob(uv, vec2(0.34, 0.66) + o2, vec2(0.34, 0.34)) * 1.00;
  dark += blob(uv, vec2(0.66, 0.56) + o3, vec2(0.46, 0.32)) * 1.10;
  dark += blob(uv, vec2(0.95, 0.66) + o4, vec2(0.30, 0.36)) * 0.80;
  dark = smoothstep(0.10, 0.9, clamp(dark, 0.0, 1.0));
  col = mix(col, navy, dark);

  // Bright soft glow, bottom-left — tighter so it doesn't wash the blue to grey.
  float g1 = blob(uv, vec2(0.08, -0.04), vec2(0.42, 0.36));
  col = mix(col, uLight, pow(clamp(g1, 0.0, 1.0), 1.7) * 0.95);

  // Softer light, top-right.
  float g2 = blob(uv, vec2(0.98, 1.05), vec2(0.40, 0.40));
  col = mix(col, ltblue, clamp(g2, 0.0, 1.0) * 0.4);

  // Punch: boost saturation + a touch of contrast so mid-tones read blue, not grey.
  float l = dot(col, vec3(0.299, 0.587, 0.114));
  col = max(mix(vec3(l), col, 1.4), 0.0);      // saturate (factor > 1)
  col = max((col - 0.5) * 1.1 + 0.5, 0.0);     // slight contrast

  gl_FragColor = vec4(col, 1.0);
}
```

`hexToVec3(hex)`: `n = parseInt(hex.slice(1),16)`, `vec3(((n>>16)&255)/255, ((n>>8)&255)/255,
(n&255)/255)`.

### Scene B — Plasma Burst (hero)

The centrepiece. `WebGL1Renderer({ antialias:true })`, `setPixelRatio(dpr)`,
`setClearColor(0x000000, 1)`, `scene.background = black`. It renders on **pure black** and the canvas
is composited over Scene A via CSS `mix-blend-mode:lighten`. `PerspectiveCamera(48, w/h, 0.1, 80)` at
`z = cameraZ`. A `Group` holds the filament `LineSegments` and spark `Points`; a camera-facing core
sprite (`PlaneGeometry(3,3)`) is added to the scene. Postprocessing: `EffectComposer` →
`RenderPass` → `UnrealBloomPass(vec2(w,h), bloomStr, bloomRadius, bloomThresh)` →
`ShaderPass(GammaCorrectionShader)`. All three materials use `AdditiveBlending, transparent:true,
depthTest:false, depthWrite:false`.

**Baked config (the shipped defaults):**

```
stageColor:#1c3ee6  glowColor:#eef3ff       // fed to Scene A behind it (base, light)
cameraZ:4  rotateSpeed:0.24                  // idle turntable spin (rad/s)
coreSize:0.39  coreGlow:0.59
bloomStr:0.44  bloomRadius:0  bloomThresh:0.11
lineBrightness:0.48  sway:0.19  swaySpeed:0.6  shimmer:0  shimmerSpeed:0.4
sparkBrightness:0.32  sparkSize:0.05  twinkle:0.1
colCore:#ffffff  colInner:#c2c6ff  colMid:#5c7cff  colOuter:#242c9e
coreColor:#a3a6ff  coreHalo:#5f5cff
filaments:60  sparks:700  spread:2.65  curl:0
```

Fixed artwork constants: `segments:64` (points per filament), `bendCfg:0.55`, `tipFade:0.14`,
`lineBright:1.1` (spec base brightness), `mainAlpha:1`, `TAU:6.2831`, spark colours
`colSpark:#ffffff`, `colPink:#e6ecff` (particles are white shades only). Line material uniforms
include `uTanHalfFov = tan((48°·π/180)/2)`, `uAspect = w/h`, `uMagnetRadius = 0.9`. Spark material
`uPixelRatio = min(dpr, 2)`.

**Geometry build (deterministic PRNG — mulberry32, seed `0x9e3779b9`).** Build filaments then sparks
from one PRNG stream.

`mulberry32(a)`:
```js
function mulberry32(a){ return function(){ a|=0; a=(a+0x6d2b79f5)|0;
  let t=Math.imul(a^(a>>>15),1|a); t=(t+Math.imul(t^(t>>>7),61|t))^t;
  return ((t^(t>>>14))>>>0)/4294967296; }; }
```

Filament gradient colours (baked into per-vertex `aColor`): `core=colCore, inner=colInner,
mid=colMid, outer=colOuter` (hex→vec3). For each of `F = 60` filaments: pick `seed=rng()`;
a random unit direction `dir` from `z=rng()*2-1, th=rng()*TAU, rr=sqrt(1-z*z)` →
`(rr·cos th, rr·sin th, z)`; build an orthonormal `u,v` from `dir` and an `up` vector
(`up = |dir.y|>0.99 ? (1,0,0) : (0,1,0)`), `u = normalize(dir×up)`, `v = normalize(dir×u)`. Length
`len = spread*(0.28 + rng()*rng()*1.15)`; if `rng()>0.86` then `len*=1.7`. Curl params
`f1=2+rng()*5, f2=2+rng()*5, p1=rng()*TAU, p2=rng()*TAU, curlF=curl*(0.45+rng())`. A `bend` vector
`= normalize(u*(rng()-0.5) + v*(rng()-0.5)) * (bendCfg*(0.4+rng()))`. Sample `P=64` points
`i=0..P-1`, `t=i/(P-1)`, `radius = coreStart + len*t` (`coreStart=0.06`):
`pt = dir*radius + u*sin(t*f1*π+p1)*curlF*t + v*cos(t*f2*π+p2)*curlF*t + bend*t*t`. Per-point colour:
`c1 = inner.lerp(mid, smoothstep(t,0,0.5))`, `c = c1.lerp(outer, smoothstep(t,0.5,1))`, then
`c.lerp(core, (1 - smoothstep(t,0,0.12))*0.9)`, then `bright = lerp(1.15, tipFade, smoothstep(t,0,1))`,
`c *= bright`. Emit as `LineSegments` (each of the `P-1` segments duplicates its two endpoints).
Per vertex attributes: `position`, `aColor`, `aSway=u`, `aSway2=v`, `aAlong=t`, `aSeed=seed`. Keep
each filament's tip point (`pts[P-1]`) in a `tipFlare[]` list for spark placement.

Sparks (`S = 700`, white shades only): for each, `roll=rng()`. If `roll<0.42` and tips exist: place
near a random tip `± (rng()-0.5)*0.18` on each axis. Else if `roll<0.88`: on a sphere,
`rad = spread*(0.12 + pow(rng(),1.4)*1.05)`. Else (bokeh): `rad = spread*(1.0 + rng()*1.2)`, z scaled
`*0.7`, flagged `isBokeh`. Colour: `distN = min(1, |pos|/(spread*1.4))`; `c = (rng()>0.82 ? pink :
spark)` then `c *= lerp(1.0, 0.35, distN) * (isBokeh ? 0.55 : 1.0)`. Size `= isBokeh ? 1.1+rng()*1.3 :
0.3 + rng()*rng()*0.85`. Attributes: `position`, `aColor`, `aSize`, `aPhase=rng()`.

**Filament vertex shader (VERBATIM):**

```glsl
attribute vec3  aColor;
attribute vec3  aSway;
attribute vec3  aSway2;
attribute float aAlong;
attribute float aSeed;
uniform float uTime, uSway, uSwaySpeed, uEnergy;
uniform vec2  uPointer;                 // cursor in NDC (-1..1), eased
uniform float uMagnet, uTanHalfFov, uAspect; // magnet strength (eased) + camera params
uniform float uMagnetRadius;            // view-space reach of the magnet
varying vec3  vColor;
varying float vAlong, vSeed;
void main(){
  vColor = aColor; vAlong = aAlong; vSeed = aSeed;
  vec3 p = position;
  float amp = uSway * aAlong * (1.0 + uEnergy * 1.6);
  float ph  = aSeed * 6.2831;
  float t   = uTime * uSwaySpeed;
  float w1 = sin(t + ph + aAlong * 5.0);
  float w2 = cos(t * 1.27 + ph * 1.7 + aAlong * 9.0);
  float w3 = 0.5 * sin(t * 0.6 + ph);
  p += aSway  * (w1 + w3) * amp;
  p += aSway2 * w2 * amp * 0.8;
  vec4 mv = modelViewMatrix * vec4(p, 1.0);
  // Magnetise filaments toward the cursor in screen (view) space — base anchored
  // at the core (aAlong=0), tips pulled most (aAlong^2). uPointer is NDC; convert to
  // the view-space xy that projects there at this vertex's depth. Applied post-
  // modelView so the lean follows the cursor on screen regardless of the spin.
  float hy = -mv.z * uTanHalfFov;
  float hx = hy * uAspect;
  vec2 targetView = vec2(uPointer.x * hx, uPointer.y * hy);
  // Only filaments whose tip is near the cursor react: Gaussian falloff on the
  // view-space distance from the vertex to the cursor. Far filaments (prox->0) stay put.
  float dist = length(targetView - mv.xy);
  float prox = exp(-(dist * dist) / (uMagnetRadius * uMagnetRadius));
  float pull = uMagnet * aAlong * aAlong * prox;
  mv.xy = mix(mv.xy, targetView, pull);
  gl_Position = projectionMatrix * mv;
}
```

**Filament fragment shader (VERBATIM):**

```glsl
precision highp float;
uniform float uTime, uShimmer, uShimmerSpeed, uBright, uBrightness, uEnergy, uShock, uShockAmp;
varying vec3  vColor;
varying float vAlong, vSeed;
void main(){
  float sh = 0.6 + uShimmer * 0.55 * sin(vAlong * 16.0 - uTime * uShimmerSpeed + vSeed * 28.0);
  sh = max(sh, 0.0);
  float shock = 0.0;
  if (uShock >= 0.0) shock = uShockAmp * exp(-pow((vAlong - uShock) * 7.0, 2.0)) * 2.0;
  vec3 c = vColor * (sh * (1.0 + uEnergy * 0.8) + shock) * uBright * uBrightness;
  gl_FragColor = vec4(c, 1.0);
}
```

**Spark vertex shader (VERBATIM):**

```glsl
attribute vec3  aColor;
attribute float aSize;
attribute float aPhase;
uniform float uTime, uSparkSize, uTwinkle, uPixelRatio, uEnergy;
varying vec3  vColor;
varying float vTw;
void main(){
  vColor = aColor;
  float tw = 0.45 + 0.55 * sin(uTime * uTwinkle + aPhase * 6.2831);
  vTw = tw;
  vec4 mv = modelViewMatrix * vec4(position, 1.0);
  float s = uSparkSize * aSize * uPixelRatio * (200.0 / max(0.001, -mv.z));
  gl_PointSize = s * (0.6 + 0.5 * tw) * (1.0 + uEnergy * 0.4);
  gl_Position = projectionMatrix * mv;
}
```

**Spark fragment shader (VERBATIM):**

```glsl
precision highp float;
uniform float uBrightness, uEnergy;
varying vec3  vColor;
varying float vTw;
void main(){
  vec2 d = gl_PointCoord - 0.5;
  float r = length(d);
  float a = smoothstep(0.5, 0.0, r);
  a *= a;
  vec3 c = vColor * (0.4 + vTw) * (1.0 + uEnergy * 0.6) * uBrightness;
  gl_FragColor = vec4(c, a);
}
```

**Core sprite vertex shader (VERBATIM):**

```glsl
varying vec2 vUv;
void main(){ vUv = uv; gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0); }
```

**Core sprite fragment shader (VERBATIM):**

```glsl
precision highp float;
uniform vec3 uColCore, uColMid;
uniform float uTime, uGlow, uBrightness, uAlpha, uEnergy;
varying vec2 vUv;
void main(){
  vec2 p = vUv - 0.5;
  float r = length(p) * 2.0;
  float core = exp(-r * r * 16.0);
  float halo = exp(-r * 4.2) * 0.22;
  float flick = 0.97 + 0.03 * sin(uTime * 1.5); // gentle breathe, no fast flicker
  float boost = 1.0 + uEnergy * 1.2;
  vec3 c = (uColCore * core + uColMid * halo) * uGlow * flick * boost * uBrightness;
  float a = clamp(core + halo, 0.0, 1.0) * uAlpha;
  gl_FragColor = vec4(c, a);
}
```

Core material uniforms: `uColCore = hex(coreColor)`, `uColMid = hex(coreHalo)`, `uGlow = coreGlow`,
`uBrightness = 1`. `uEnergy`, `uShock`, `uShockAmp` stay 0 (unused in the shipped hero). `uShimmer = 0`.

**Per-frame loop.** Constants: `SPIN_MULT = 5`, `ZOOM_MULT = 1.7`, `INTRO_MS = 1400`,
`INTRO_SCALE_FROM = 0.45`, `INTRO_SPIN = 3.2`, `easeOutCubic(t)=1-(1-t)^3`, `MAGNET_STRENGTH = 0.38`,
`MAGNET_EASE = 0.08`, `POINTER_EASE = 0.12`, `MAGNET_RADIUS = 0.9`.
- **Intro gate:** the burst stays hidden (alpha 0, scaled down) until the preloader is done, then
  plays once: `introP = clamp01((now - introStart)/INTRO_MS)`, `introE = easeOutCubic(introP)`,
  `alpha = introE`.
- **Spin & zoom:** `hp = heroProgress()` (hero-shrink 0..1). `introSpin = (1-introP)*INTRO_SPIN`.
  `spin += (rotateSpeed*(1 + hp*(SPIN_MULT-1)) + introSpin) * dt`; `group.rotation.y = spin`.
  `group.scale = lerp(INTRO_SCALE_FROM, 1, introE)`. `camera.position.z = cameraZ / (1 + hp*(ZOOM_MULT-1))`.
- **Cursor magnet:** track the pointer on `window` (pointermove), convert to canvas-rect NDC
  (`x = (px-left)/w*2-1`, `y = -((py-top)/h*2-1)`), `active` = inside the canvas rect. Each frame:
  `targetMag = active ? MAGNET_STRENGTH : 0`; `magnet += (targetMag-magnet)*MAGNET_EASE`; if active,
  ease `pX,pY` toward the pointer NDC by `POINTER_EASE`. Set `uMagnet = magnet`, `uPointer = (pX,pY)`.
- Core sprite faces the camera (`quaternion.copy(camera.quaternion)`), `scale = coreSize`. Set
  `uTime = now` and `uAlpha = alpha` on all three materials. `composer.render()`. Clamp `dt ≤ 0.05`.

### Scene C — Chrome model (chain + footer heart)

`WebGLRenderer({ antialias:true, alpha:true })`, `setPixelRatio(min(dpr,2))`, `setClearColor(0x000000,0)`
(transparent — the gradient shows behind), `outputEncoding = sRGBEncoding`,
`toneMapping = ACESFilmicToneMapping`, `toneMappingExposure = exposure`. `PerspectiveCamera(42, w/h,
0.1, 100)` at `(0,0,8)`. Environment: `PMREMGenerator.fromScene(new RoomEnvironment(), 0.04)` →
`scene.environment`. A `DirectionalLight(0xffffff, 2.2)` at `(4,6,5)`. Chrome material
(`MeshStandardMaterial`), **baked defaults**: `color:#a5ade3, metalness:1, roughness:0.1,
envMapIntensity:2.8, exposure:0.2`.

Load the GLB via `GLTFLoader` + `DRACOLoader` (`setDecoderPath(
"https://www.gstatic.com/draco/versioned/decoders/1.5.6/")`). On load: centre the model on origin
(subtract its bounding-box centre), `scale = 3.6 / maxDim` (fit to ~3.6 world units), replace every
mesh material with the shared chrome material. Group `rotation.x = TILT_X = 0.22` (fixed viewing tilt).

Constants: `IDLE_SPIN = 0.004` (rad/frame idle turn), `SCROLL_ACCEL = 0.00009`, `FRICTION = 0.965`,
`MAX_SPIN = 0.22`, `FALL_TOP = 9` (world Y at progress 0), `FALL_BOTTOM = -9` (world Y at progress 1).
Per frame: `p = clamp01(progress())`; `group.position.y = FALL_TOP + (FALL_BOTTOM-FALL_TOP)*p`. Spin:
if `scrollSpin` (chain), `spinVel = spinVel*FRICTION + (scrollY - lastScroll)*spinAccel`, clamp to
`±maxSpin`, `group.rotation.y += spinDirection*(IDLE_SPIN + spinVel)`; else constant
`group.rotation.y += spinDirection*IDLE_SPIN`. **Chain** uses `chain.glb`, `scrollSpin:true`,
`spinAccel:0.00004`, `maxSpin:0.045`, `spinDirection:+1`. **Footer heart** uses `heart.glb`,
`scrollSpin:false` (constant), `spinDirection:-1`.

## The loader / reveal

The **Preloader** is a full-screen white overlay (`position:fixed; inset:0; z-index:100;
overflow:hidden; background:#fff`) that plays first, then slides up to reveal the hero. Everything is
driven from the shared ticker off one value `m` (0→1). Timing: `LOAD = 1900ms`, `EXIT = 950ms`,
`TOTAL = 2850ms`, `A = LOAD/TOTAL ≈ 0.6667`. `BAR_PX = 64`, `thin = clamp01(64/innerHeight)`.
`clamp01`, `easeOutQuart(t)=1-(1-t)^4`, `easeInOutQuart(t)= t<0.5 ? 8t⁴ : 1 - (-2t+2)⁴/2`.

Derived each frame: `v = clamp01((time - t0)/TOTAL)`, `fill = easeOutQuart(clamp01(v/A))`,
`exit = easeInOutQuart(clamp01((v-A)/(1-A)))`.

- **Overlay** transform: `translateY(-exit*100%)` (slides up as it exits).
- **Centre logo:** `position:absolute; inset:0; display:flex; align-items:center; justify-content:center;
  color:#000` — a bolt icon (28×28) + **"Stride"** `font-size:1.875rem; font-weight:500; gap:8px`.
- **Counter:** `position:absolute; bottom:4rem` (`md`:5rem); `left = max(fill,0.16)*100%`,
  `transform:translateX(-100%)` (tracks the fill edge). A `<span>` `font-size:3rem`→`sm:3.75rem`→
  `lg:6rem`; `font-weight:300; line-height:0.95; letter-spacing:-0.02em; color:#000; padding-right:
  1.5rem; white-space:nowrap` whose text is `round(easeOutQuart(clamp01(v/A))*100)` + "%" (0→100%),
  written imperatively.
- **Bottom bar:** an outer `position:absolute; inset-x:0; bottom:0; height:100lvh; transform-origin:
  bottom; overflow:hidden` with `transform: scaleY(thin + (1-thin)*exit)` (renders as a ~64px strip
  while loading, then grows to full height). Its inner `height:100%; width:100%; transform-origin:
  left; background:linear-gradient(to right, var(--preloader-from), var(--preloader-to))` with
  `transform: scaleX(fill)` (fills left→right).

**Release:** at `DONE_AT = A + (1-A)*0.6` (partway through the slide-up) fire the "done" flag that
releases the **hero title intro** and the **plasma burst intro** — so they begin playing as the loader
is still clearing (no dead pause). At `v ≥ 1` unmount the overlay. Reduced motion → skip straight to
the revealed page.

This preloader-clear into the spun-up, zooming plasma burst behind the rising white bar is the hero
moment the preview captures — get its timing and the gradient bar's grow-and-slide exactly right.

## Fixed parameters (bake these in)

- **Colours:** hero-base `#04070f`, hero-stage `#1246e2`, hero-glow `#2ad4ff`, hero-page/white
  `#ffffff`, logo `#c6ccd6`, accent-blue `#2e9bff`, accent-lime `#b6f13a`, muted `#9aa1ad`, card-blue
  `#2f7cf6`, card-gray `#ededf0`, card-dark `#0a0c12`, plum `#241c3a`, lilac `#d7d1f2`, paper
  `#f5f4f8`, footer `#0a1533`, footer-fg `#f2f4f1`, footer-muted `#aeb9d8`, preloader-from `#d7e0ff`,
  preloader-to `#1b34e8`. Gradient/model palette: base `#1c3ee6`, light `#eef3ff`; chrome `#a5ade3`.
- **Font:** Mulish 300/400/500. **Page gutter:** 120px (lg). **Root scaling:** as in Page shell.
- **Hero:** `INSET 24px`, `RADIUS 24px`, `SHRINK_VH 0.6`, UI `scale(1 - p*0.1)`; title cascade
  `DURATION 1600, SPREAD 0.55, RISE 16, BLUR 8`; non-heading reveal `RISE 20, BLUR 10`, 6 items.
- **Reveal cascade (headings/cards):** `DURATION 1400, SPREAD 0.55, RISE 16, BLUR 8`, easeOutQuart;
  cards `CARD_RISE 24, CARD_BLUR 12`. Chips `CHIP_MIN_SCALE 0.3`.
- **Logo marquee:** `COPIES 6`, `ONE_SET 100/6`, speeds left `0.005`, right `0.0038`, spring
  `tension 90, friction 34`.
- **Works:** `count 6`, `SCROLL_PER_CARD 60vh` (→ `height 400vh`), `RADIUS 1350`, `STEP 27`,
  `OVERSCAN 0.2`, follow-lerp `0.14`, card opacity `1 - d*0.24` (cull `d>3.6`), scrim
  `min(0.35, d*0.22)`, backdrop `translateY((i-f)*22%) scale(1.6)`, perspective `1900px`.
- **Plasma burst:** cameraZ 4 (fov 48), rotateSpeed 0.24, coreSize 0.39, coreGlow 0.59, bloom
  `str 0.44 / radius 0 / thresh 0.11`, lineBrightness 0.48, sway 0.19, swaySpeed 0.6, shimmer 0,
  sparkBrightness 0.32, sparkSize 0.05, twinkle 0.1, filaments 60, sparks 700, spread 2.65, curl 0,
  segments 64, `SPIN_MULT 5`, `ZOOM_MULT 1.7`, `INTRO_MS 1400`, `INTRO_SCALE_FROM 0.45`,
  `INTRO_SPIN 3.2`, `MAGNET_STRENGTH 0.38 / EASE 0.08 / RADIUS 0.9`, `POINTER_EASE 0.12`. Colours as
  in the config block. Filament/spark/core colours baked; sparks white-only.
- **Gradient bg seeds:** hero 0, chain 3.7, footer 7.2.
- **Chrome model:** color `#a5ade3`, metalness 1, roughness 0.1, envMapIntensity 2.8, exposure 0.2,
  fov 42, camera z 8, model fit 3.6 world units, `TILT_X 0.22`, `IDLE_SPIN 0.004`, `FALL_TOP 9`,
  `FALL_BOTTOM -9`; chain `scrollSpin:true, spinAccel 0.00004, maxSpin 0.045, dir +1`; heart
  `scrollSpin:false, dir -1`. Chain fall low-pass `0.12`.
- **Preloader:** `LOAD 1900, EXIT 950, TOTAL 2850, A≈0.6667, DONE_AT = A+(1-A)*0.6, BAR_PX 64`.
- **Smooth scroll lerp:** `0.08`.

## Assets

`ASSET_BASE_URL = https://api.getlayers.ai/storage/v1/object/public/public/assets/stride-692ad1c86d`

Load every asset from its full URL below (keep the same in-scene usage — GLB → chrome material,
`<img>` → cover). GLBs are Draco-compressed; decode with the gstatic decoder path above.

- Chain model: `…/assets/chain.glb`
- Heart model: `…/assets/heart.glb`
- Product wide card photo: `…/assets/images/6th.png`
- Stats people photo: `…/assets/images/2nd/people.png`
- Stats avatars: `…/assets/images/2nd/avatars/1.png`, `…/2.png`, `…/3.png`, `…/4.png`
- Showcase columns: `…/assets/images/3rd/approach.png`, `…/assets/images/3rd/technology.jpg`,
  `…/assets/images/3rd/security.jpg`, `…/assets/images/3rd/team.jpg`
- Portfolio cards: `…/assets/images/portfolio/1.jpg` … `…/assets/images/portfolio/6.jpg`

Full URLs (prefix each with `ASSET_BASE_URL`):
`/assets/chain.glb`, `/assets/heart.glb`, `/assets/images/6th.png`, `/assets/images/2nd/people.png`,
`/assets/images/2nd/avatars/1.png` … `/4.png`, `/assets/images/3rd/approach.png`,
`/assets/images/3rd/technology.jpg`, `/assets/images/3rd/security.jpg`, `/assets/images/3rd/team.jpg`,
`/assets/images/portfolio/1.jpg` … `/6.jpg`.