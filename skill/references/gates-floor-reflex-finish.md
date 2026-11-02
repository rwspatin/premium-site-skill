# Quality gates: Floor · Reflex · Finish

One list of what a premium build refuses, tiered by whether you may disagree. Runs at shared
workflow step 5 (Polish/QA) and again before "done". Mechanical checks live in
`ai-tell-lint.md`; viewport loop in `responsive-doctrine.md`; judged checks in the blind pass of
`design-world-never-protocol.md`. This file says how much each finding BINDS.

## Tiers and grades
- **Floor** — never waivable. Correctness, access, honesty, the Blueprint, the perf budget, and
  every item of the project's own `DESIGN_WORLD.md` NEVER list (declared exceptions excepted).
- **Reflex** — a default a model falls into. Fix it, or waive it with ONE logged line.
- **Finish** — craft detail invisible at arm's length. Reported and answered, never blocking.
Grades: `FAIL` (open Floor) · `REFLEX` (unanswered Reflex) · `WAIVED` · `ANSWERED` (Finish with a
reason). Report FAIL and REFLEX counts separately so "0 FAIL" stays a true sentence.
**Nothing ships with an open FAIL or an unanswered REFLEX.**

## Waiver line (Reflex only)
Log in `DESIGN_WORLD.md` § Waivers (or a CSS comment at the top of the stylesheet):
`waive R3 · <evidence the guard holds> · <reason in this page's own nouns>`
- The reason names something on THIS page; boilerplate ("brand choice", "looks good") is void.
- Waiving a Floor gate is void; the gate still fails.
- Same Reflex waived on 3 consecutive projects = a house style: promote it into the Blueprint or
  the project's DESIGN_WORLD, stop waiving it.

## Floor (F) — nothing ships
- **F1 Tokens**: no hex/rgb/px/font-family literal outside `:root`; no `#000`/`#fff`; spacing only
  from the 8pt scale; one easing (`--ease-signature`), no bounce/spring on UI state; 2 families max.
- **F2 390px**: no horizontal overflow at 320–1920 (`scrollWidth <= innerWidth`); every
  `sm:/md:/lg:` styling utility has its base pair; 390/768/1440 screenshots LOOKED at.
- **F3 Visible at rest**: content readable with JS off; reveal `opacity:0` only under a `.js`
  class or `@starting-style`; nothing gated behind an intro.
- **F4 Perf budget** (SKILL.md): hero ≤ 250 KB, above-fold ≤ 600 KB, LCP ≤ 2.5 s mobile; LCP
  element never `loading="lazy"`, gets `fetchpriority="high"`; video `autoplay muted loop
  playsinline` + poster; one rAF loop, paused offscreen/hidden; ≤ 6 `backdrop-filter`.
- **F5 Motion safety**: transform/opacity only; no `transition: all`; every keyframe/transition
  has a `prefers-reduced-motion: reduce` fallback; auto-advancing content pauses on hover+focus.
- **F6 Access**: body text 4.5:1, large text/icons/focus rings/field edges 3:1; `:focus-visible`
  on every interactive, instant (never faded in); hit areas ≥ 44px; decorative `svg`/`canvas`
  `aria-hidden`; no emoji as icons; `lang`, `<title>`, meta description; no skipped heading level
  that breaks the outline.
- **F7 Honesty**: no invented metric, testimonial, logo wall, case count; no form that prints
  success with no request behind it; no hand-drawn browser/OS chrome.
- **F8 Structure**: no card inside a card; hero height in `dvh`/`svh`, never `vh`; button and
  primary-nav labels never wrap; image grid tracks `minmax(0,1fr)`; no two `sticky; top:0`.
- **F9 Hard AI tells**: purple→blue/pink or cyan-on-dark gradients, gradient text; buzzword copy
  (`ai-tell-lint.md` §Copy); > 1 glow per screen.
- **F10 Redesign kept-contract** (below).

## Reflex (R) — fix or waive with a line
R1 template anatomy (hero → 3 features → CTA → footer) · R2 three equal icon-over-heading tiles ·
R3 an eyebrow over every section (Blueprint labels/chips stay legal; the per-section kicker does
not) · R4 one accented/italic word in a roman heading · R5 every seam separated the same way, same
gap · R6 stock chrome (logo-left/links/button-right nav; 4-column footer) not decided on purpose ·
R7 email sign-up as the close when the page's job is not subscribing · R8 mono on labels/meta with
no code or data · R9 accent past ~5% of a viewport · R10 prose measure outside 45–75ch ·
R11 framework default palette (indigo-500, violet-600, slate-900) · R12 same hover lift on every
card, or images scaling on hover · R13 device/laptop/phone frame around fake UI · R14 drawn SVG
illustration where a real asset or the product belongs · R15 centred 4-stack hero (title, lede,
CTA, mark all on one axis) · R16 a fact (price, date, count) repeated in 4+ sections.

## Finish — answer, never block
Curly quotes and `…` · tabular numerals on display figures · `text-wrap: pretty` on prose ·
`::selection`, `caret-color`, `scrollbar-color` themed · no one-word orphans at 390 · one filled
primary-button recipe per page · one scroll-choreography pattern per page · flex rows of unequal
height aligned · no lone item on a grid's last row · helper-text slots reserve `min-height: 1lh`.

## F10 · Redesign kept-contract (before/after diff)
On ANY redesign or restyle, prove nothing functional was lost. Capture from the OLD build
(`git worktree add --detach ../before main`, serve on another port) and the new one, same routes:
```bash
cat <<'EOF' | agent-browser eval --stdin > kept-before.json
JSON.stringify({
  links: [...document.querySelectorAll('a[href]')].map(a => a.getAttribute('href')).sort(),
  fields: [...document.querySelectorAll('input,select,textarea')].map(f => `${f.form?.action||''}|${f.name}|${f.type}|${f.required}`).sort(),
  forms: [...document.querySelectorAll('form')].map(f => `${f.action}|${f.method}`).sort(),
  hooks: [...document.querySelectorAll('[id],[data-testid]')].map(e => e.dataset.testid || '#' + e.id).sort(),
  buttons: [...document.querySelectorAll('button,[role=button]')].map(b => (b.textContent||'').trim() || b.getAttribute('aria-label')).sort(),
  scripts: [...document.scripts].map(s => s.src).filter(Boolean).sort() })
EOF
```
Repeat → `kept-after.json`, diff. React handlers are invisible to the DOM: also run
`git diff main -- src | grep -E '^-.*(onClick|onSubmit|action=|href=|name=|data-testid)'` and
account for every removed line. Anything missing is `FAIL kept` unless restored or logged:
`dropped /blog, #team · owner is retiring the blog`. Report one line:
`Kept 14/14 links · 5/5 fields · 3/3 forms · 9/9 hooks · dropped: none`.
