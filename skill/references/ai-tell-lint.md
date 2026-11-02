# AI-tell lint — mechanically detectable slop

The tells a machine can find without taste: grep the source, measure the rendered DOM. Feeds the
code-scan pass of `design-world-never-protocol.md` §2 and the tiers in
`gates-floor-reflex-finish.md` (F = Floor, R = Reflex). The blind screenshot pass still runs —
this list catches what pixels hide, not what only an eye judges.

**Blueprint wins on conflict.** Legal by our tokens: uppercase tracked labels/chips/buttons
(Blueprint §3), Fraunces display + Inter body, pill CTA radius. Identity a brand already owns is
kept (design-world gotcha). Flag the reflex, not the system.

## Run it
```bash
agent-browser open http://localhost:3000 && agent-browser set viewport 1440 900
cat <<'EOF' | agent-browser eval --stdin      # one probe per check below; repeat at 390 844
JSON.stringify([...document.querySelectorAll('*')].filter(e => /text/.test(getComputedStyle(e).webkitBackgroundClip)).map(e => e.tagName + '.' + e.className))
EOF
```
Scroll each section into view before measuring (reveals change computed opacity/transform).

## Colour and surface
| Tell | Grep (source) | Measure (DOM) | Tier |
|---|---|---|---|
| Purple/violet→blue/pink, cyan-on-dark gradients | `from-(purple\|violet\|fuchsia\|indigo)`, `linear-gradient` with hue 260–330 stops | computed `background-image` gradient stops → OKLCH hue | F9 |
| Gradient text | `bg-clip-text`, `background-clip:\s*text` | `webkitBackgroundClip === 'text'` | F9 |
| Pure black/white | `#000\b`, `#fff\b`, `text-black`, `bg-white` outside `:root` | computed `rgb(0, 0, 0)` / `rgb(255, 255, 255)` on color/bg | F1 |
| Glow / orbs / mesh blobs | `shadow-\[0_0_`, `blur-3xl`, `radial-gradient` on absolute divs | count `box-shadow` with 0 0 offset + chroma per viewport; > 1 fails | F9 |
| Framework stock palette | `indigo-500`, `violet-600`, `slate-900`, `zinc-950` | — | R11 |
| Accent sprawl | — | area of elements whose bg/fill = `--accent` ÷ viewport area > 5% | R9 |

## Type
| Tell | Grep | Measure | Tier |
|---|---|---|---|
| > 2 families | `font-family`, `next/font` imports | `new Set(all computed fontFamily first token)` | F1 |
| Accented word in a heading | `<h[1-3][^>]*>[^<]*<(em\|i\|span)` | heading child whose color/fontStyle/weight ≠ parent | R4 |
| Eyebrow over every section | — | element just before each `h2`: ≤ 14px, uppercase or tracking > .03em, < 40 chars; count ≥ 3 sections | R3 |
| Mono as costume | `font-mono` on non-code | mono computed on elements with no `code`/digits | R8 |
| Prose measure | `max-w-` on `p` | `p` width ÷ `ch` outside 45–75 | R10 |

## Layout and structure
| Tell | Grep | Measure | Tier |
|---|---|---|---|
| Hero in `vh` | `h-screen`, `min-h-screen`, `100vh` | — (use `dvh`/`svh`) | F8 |
| Horizontal overflow | `w-screen`, `100vw` | `documentElement.scrollWidth > innerWidth` at 320/390/1440 | F2 |
| Card in card | — | bordered/shadowed box with a bordered/shadowed descendant box | F8 |
| Three equal icon tiles | `grid-cols-3` + icon component | grid with 3 equal-width children each starting svg/img → heading | R2 |
| Template anatomy | — | section sequence hero → 3-up → CTA band → footer | R1 |
| Same seam everywhere | — | every section gap / border identical | R5 |
| Wall of words | — | > 80 words in any 900px window at 1440 | R (judge) |

## Chrome and copy
- **Buzzwords (F9)**, grep case-insensitive: EN `seamless|robust|empower|supercharge|elevate|
  effortless|unlock|unleash|next-gen|world-class|best-in-class|cutting-edge|game-chang|
  revolutioni|in today's|built for the modern`; PT-BR `revolucion|de ponta|sem complica|
  potencializ|eleve|transforme seu|solução completa|no mundo de hoje|próximo nível`.
- **Placeholders (F7)**: `Jane Doe|John Doe|Fulano|Acme|Lorem|example\.com|\+55 \(00\)`.
- **Stock second CTA (R)**: `Learn more|Saiba mais|Get started|Comece agora` beside the primary.
- **Template punctuation (R)**: two+ ` · ` in one meta string; arrow glued to label (`→</`);
  decorative status dots before labels.
- **Emoji as icons (F6)**: `\p{Extended_Pictographic}` inside `button`, `li > span`, headings.
- **Fake send (F7)**: success copy (`Enviado|Obrigado|Thanks|You're on the list`) in a component
  with no `fetch`/action/server call.

## Template decoration (R unless noted)
Scroll cues (`Scroll`, mouse icon, bouncing chevron) · rotated vertical text with no fact ·
`BETA`/version pill in the hero · `01 / 04` counters on tiles · decorative grid/dot backgrounds
with nothing measured under them · bento grid as default layout · icon in a coloured coin ·
thick coloured left border on cards · hand-drawn browser bar/traffic lights (F7) · device frame
around styled-div "product UI" (R13) · "Trusted by" strip of logos nobody supplied (F7).

## Motion
`transition-all|transition:\s*all` (F5) · `ease: "back|elastic|bounce"`, `type: "spring"` on UI
state, `cubic-bezier` with a y > 1 (F1) · animated `width|height|top|left|margin` (F5) · a
`@keyframes`/`motion` file with no `prefers-reduced-motion`/`useReducedMotion` (F5) ·
`opacity-0` initial class without a `.js`/hydration guard (F3).

## Optional script spec (not built — write only when a 3rd project needs it)
`scripts/tell-lint.mjs <src-dir> [--url <dev-url>] [--viewport 390x844,1440x900]`
- **Static tier**: ripgrep the patterns above over `src/**`, `*.css`; skip `:root` blocks and
  files listed in `DESIGN_WORLD.md` exceptions.
- **Render tier** (`--url`): drive agent-browser (or Playwright) per viewport, scroll each section
  into view, run the DOM probes, screenshot offenders with the element outlined.
- **Output**: JSON lines `{id, tier, where: file:line | selector, evidence, viewport}` + a summary
  `FAIL n · REFLEX n · WAIVED n`; reads waivers from `DESIGN_WORLD.md` § Waivers.
- **Exit 1** on any open Floor; Reflex never changes the exit code. Numbers rank, eyes decide.
