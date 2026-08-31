# Sales LP anatomy — GRIGOLETTO style

# Premium LP (GRIGOLETTO style)

Reference: https://figma-templates-lp.vercel.app/ — a **single static `index.html`** (no framework, no libs):
DM Serif Display + Inter, dark bg + glass cards + one orange accent, IntersectionObserver reveal,
horizontal snap gallery, lightbox, sticky price card. Extracted CSS: `references/grigoletto-lp-extracted.css`.
Everything "expensive" here comes from **restraint + coherence**, not from libraries.

## Workflow (skills to invoke, in order — only the one for the current step)
1. **Copy first** — `/copywriting` (headline, sub, bullets, CTA, FAQ) and, if positioning/offer is weak, `/viral-product-strategist`. Write copy before markup.
2. **Tokens** — start from `~/.claude/design-references/design-system-blueprint.md`; pick the **dark** or **light** `:root` below. `design-system` skill only if the user needs a multi-page system.
3. **Build** — `frontend-design` for the markup, using the section anatomy below. `ui-pattern` + `make-interfaces-feel-better` for cards/forms/hit-areas.
4. **Motion** — plain CSS reveal (below) is enough for static HTML. For React/Next: `motion-foundations` → `motion-patterns` (never `motion-advanced` unless drag/gesture/SVG-draw is requested).
5. **Polish/QA** — `impeccable` (critique pass), `ui-review`, `accessibility-compliance`, then `seo` (meta, OG, schema Product/FAQ, CWV).
6. **Verify** — run it, `agent-browser screenshot` at 390px and 1280px, look at the PNGs. Never claim done without looking.

## Stack decision
- Default: **one `index.html`** (inline `<style>` + `<script>`), Google Fonts, deploy to Vercel. Fastest, zero build, exactly like the reference.
- Next.js/React only if there's checkout/API, CMS, or multiple pages. Then: Tailwind with tokens as CSS vars, `motion/react`, and optionally components from Magic UI / Aceternity / SkiperUI — always re-skinned to the tokens (see [ui-libs] memory).
- Optional libs for static HTML: **Lenis** (smooth scroll) and **GSAP ScrollTrigger** only if the user asks for cinematic scroll. Don't add by default.

## Section anatomy (copy this order)
```
header   .hdr  brand (serif, letter-spacing) · nav 3–4 uppercase links · pill (@handle / CTA)
hero     min-height:100svh · kicker (line + uppercase accent) · h1 serif clamp(34px,9vw,92px) lh .98 max 22ch
         · sub 18px muted max 42ch · 2–3 ✓ assurance bullets · media 16/9 glass frame with "live" chip
         · CTA primary (full width mobile, max 420px) + ghost "ver ↓"
#pack    section-head (kicker + h2 serif + lead) · shop-hint · grid 2-col (4 on desktop) of .tcard glass
         (thumb 3/4 → num 01 + name) · mid-cta
#galeria horizontal scroll-snap gallery, tiles 78% width 3/4 ratio, optional video tiles w/ play → lightbox
#para-quem 3–4 .aud cards (icon + title + text)
#como-funciona numbered steps (step__n serif big + text)
#preco   centered .price-card.glass.strong: label · strike · big price (serif) · meta dots · badges · proof · trust
#faq     <details> accordion list
footer   brand · handle · copy · legal links
```
Sections: `padding:96px 0` (64 mobile), `.wrap{max-width:1200px;padding:0 20px}`, `scroll-margin-top:72px`.

## Tokens — DARK (reference)
```css
:root{
  --accent:#ff6711; --accent-bright:#ff8200;           /* ONE accent. Use only on kicker, CTA, dots, glow */
  --bg:#0b0b0c; --bg-2:#101012; --bg-3:#16161a;        /* never #000 */
  --fg:#f2efe9; --fg-mute:rgba(242,239,233,.62); --fg-dim:rgba(242,239,233,.42);
  --glass-bg:rgba(255,255,255,.06); --glass-bg-strong:rgba(255,255,255,.09);
  --glass-bd:rgba(255,255,255,.10); --glass-bd-strong:rgba(255,255,255,.16); --glass-blur:16px;
  --f-display:"DM Serif Display",Georgia,serif;        /* or Fraunces (Blueprint) */
  --f-body:"Inter",system-ui,sans-serif;
  --r-sm:12px; --r-md:20px; --r-lg:24px; --r-xl:28px; /* never 0 */
  --ease:cubic-bezier(.625,.05,0,1); --ease-soft:cubic-bezier(.22,1,.36,1);
  --t-xs:12px; --t-sm:14px; --t-base:16.5px; --t-md:18px; --t-lg:22px; --t-xl:28px; --t-2xl:38px; --t-3xl:54px;
}
body{background:radial-gradient(1200px 600px at 80% -10%,rgba(255,103,17,.10),transparent 60%),var(--bg);color:var(--fg);font:var(--t-base)/1.5 var(--f-body);-webkit-font-smoothing:antialiased}
```
## Tokens — LIGHT (Blueprint "paper")
```css
:root{ --accent:#2F6FED; --accent-hover:#4C82FF; --accent-pressed:#1E5AD6;
  --bg:#FAFAF8; --bg-2:#F4F3EF; --fg:#16181D; --fg-soft:#1D2027; --fg-mute:#8B8F98;
  --hairline:rgba(22,24,29,.12); --surface-dark:#0E0F12;
  --f-display:"Fraunces",Georgia,serif; --f-body:"Inter",system-ui,sans-serif;
  --r-input:8px; --r-card:16px; --r-btn:24px; --r-pill:999px;
  --sh-sm:0 1px 2px rgba(16,18,29,.06); --sh-md:0 8px 24px rgba(16,18,29,.10); --sh-lg:0 20px 50px rgba(16,18,29,.16);
  --ease:cubic-bezier(.16,1,.3,1); }
body{background:linear-gradient(180deg,var(--bg),var(--bg-2))}
```
Spacing scale in both: **8/16/24/32/48/64** (section 64–96, card 24, gap 12–16).

## Core components (dark; mirror with light tokens)
```css
.glass{background:var(--glass-bg);border:1px solid var(--glass-bd);border-radius:var(--r-lg);
  backdrop-filter:blur(var(--glass-blur)) saturate(120%);-webkit-backdrop-filter:blur(var(--glass-blur)) saturate(120%);position:relative}
.glass::before{content:"";position:absolute;inset:0 0 auto 0;height:1px;
  background:linear-gradient(90deg,transparent,rgba(255,255,255,.22),transparent);pointer-events:none} /* top highlight */
.kicker{font-size:var(--t-xs);letter-spacing:2.4px;text-transform:uppercase;color:var(--accent);display:inline-flex;gap:12px;align-items:center}
.kicker::before{content:"";width:24px;height:1px;background:var(--accent)}
h1,h2{font-family:var(--f-display);font-weight:400;letter-spacing:.5px}
h1{font-size:clamp(34px,9vw,92px);line-height:.98;max-width:22ch}
.btn{display:inline-flex;align-items:center;justify-content:center;gap:10px;padding:16px 24px;border-radius:999px;font-weight:500;font-size:var(--t-sm);
  transition:transform .35s var(--ease),box-shadow .35s var(--ease),background .35s var(--ease)}
.btn--primary{background:linear-gradient(180deg,var(--accent-bright),var(--accent));color:#fff;
  box-shadow:0 10px 36px rgba(255,103,17,.35),0 0 0 1px rgba(255,255,255,.10) inset,0 -1px 0 rgba(0,0,0,.18) inset}
.btn--primary:hover{transform:translateY(-1px) scale(.99);box-shadow:0 14px 44px rgba(255,103,17,.45),0 0 0 1px rgba(255,255,255,.14) inset}
.btn--primary:active{transform:scale(.97)}
.btn--ghost{background:transparent;border:1px solid var(--glass-bd);color:var(--fg-mute)} .btn--ghost:hover{color:var(--fg);border-color:var(--glass-bd-strong)}
.tcard{padding:12px 12px 20px;display:flex;flex-direction:column;gap:22px;border-radius:var(--r-md);
  transition:transform .4s var(--ease),border-color .4s var(--ease),background .4s var(--ease)}
.tcard:hover{transform:translateY(-2px);border-color:var(--glass-bd-strong);background:var(--glass-bg-strong)}
.gallery{display:flex;gap:14px;overflow-x:auto;scroll-snap-type:x mandatory;padding:4px 20px 24px;margin:0 -20px;scrollbar-width:none}
.gtile{flex:0 0 78%;aspect-ratio:3/4;border-radius:var(--r-lg);scroll-snap-align:center;overflow:hidden;border:1px solid var(--glass-bd)}
@media(min-width:900px){.gtile{flex-basis:32%}}
.price-card{max-width:520px;margin:0 auto;padding:36px 26px 32px;text-align:center;border-radius:var(--r-xl)}
.price__big{font-family:var(--f-display);font-size:var(--t-3xl)} .price__strike{text-decoration:line-through;color:var(--fg-dim)}
```
## Motion (mandatory rules)
```css
.reveal{opacity:0;transform:translateY(28px);transition:opacity .9s var(--ease),transform .9s var(--ease)}
.reveal.in{opacity:1;transform:none}
.reveal[data-d="1"]{transition-delay:.12s}.reveal[data-d="2"]{transition-delay:.24s}.reveal[data-d="3"]{transition-delay:.36s}
@keyframes rec{0%,100%{opacity:1}50%{opacity:.35}}  /* "live" dot */
@media(prefers-reduced-motion:reduce){*{animation:none!important;transition:none!important}.reveal{opacity:1;transform:none}}
```
```js
const io=new IntersectionObserver(es=>es.forEach(e=>{if(e.isIntersecting){e.target.classList.add('in');io.unobserve(e.target)}}),{threshold:.12,rootMargin:'0px 0px -8% 0px'});
document.querySelectorAll('.reveal').forEach(el=>io.observe(el));
```
- Easing only `--ease`/`--ease-soft`; UI .3–.5s; entrances .9–1.4s; stagger .1–.2s; hover = opacity .6 or translateY(-1/-2px); CTA active scale(.97). **No bounce/spring, no parallax by default.**
- One glow per screen (CTA shadow or radial bg). Uppercase only in kicker/labels/nav/buttons.

## Data-driven content
Put templates/products/FAQ in a JS `const ITEMS=[{name,slug,cover,count,video}]` and render the grid/gallery/lightbox from it (the reference does this). Images: `.webp`, `loading="lazy"`, explicit `aspect-ratio` to avoid CLS. Videos: `muted playsinline loop preload="metadata"` + poster.

## Conversion essentials (don't skip)
Kicker → outcome-headline → 1-line sub → 2–3 ✓ assurances (price/risk/plan-free) → CTA above the fold; repeat CTA mid-page and after price; price card with strike + badges + proof + trust line; FAQ answering objections; pixels (Meta/TikTok/GA) in `<head>` and `fbq('track','InitiateCheckout')` on CTA; UTM passthrough to checkout link.

## Final checklist
- [ ] Only `var(--token)` values; spacing on the 8-scale; radii never 0; one accent, one glow
- [ ] h1 ≤ 22ch, sub ≤ 42ch, body 16.5–18px/1.5
- [ ] 5 states on interactive elements (default/hover/active/focus-visible/disabled); focus ring visible
- [ ] Reveal on every section child; reduced-motion honored
- [ ] 390px and 1280px screenshots reviewed; no horizontal overflow; gallery snaps
- [ ] `<title>`, meta description, OG image, `Product` + `FAQPage` JSON-LD, lang="pt-BR"

## Immersive / "3D" hero
If the user asks for a hero "como o do Grigoletto/Figma templates", cenas 3D, cards flutuando, profundidade, parallax imersivo — read `references/immersive-3d-hero.md` first. Rule: the LP stays flat; only the hero media slot gets the immersive scene (video → CSS 3D layers → depth-map shader → R3F, cheapest that sells it).

## Kinetic identity sites (athlete/founder/creator)
For a personality site at the landonorris.com level — acid accent, type mixing, Rive-style micro-motion, wireframe-over-photo hero — read `references/offbrand-kinetic-site.md`. Different register from a sales LP: identity + editorial, not conversion anatomy.
