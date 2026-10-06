# Design World + NEVER list (anti-sameness protocol)

How to keep an AI-built site from converging on the generic look: declare the world and the
forbidden list BEFORE code, critique against it with a reviewer who never saw the code,
then fix at the token level. Proven on renanspatin.com (2026-10-06, PR rwspatin/profissional_site#15).

Sources: replies to a 78K-view X post on "every AI UI looks the same" — start from design,
not code; close the system and a written "forbidden" list first; a NEVER list + a declared
"Design World" before any code; separate critique from construction
([oh-my-design](https://github.com/3x-haust/oh-my-design), @3xhaust_).

## 1. Write `DESIGN_WORLD.md` in the project (before any edit)
- **The world in one sentence** — a physical scene, not adjectives. "A Roman colonnade at
  night: obsidian stone, a single brass lamp, an engineer's ledger open on the table."
- **5–7 principles**, each tied to a token: "Brass is light, not paint (≤10% of a screen)",
  "Serif speaks, sans explains, mono records", "The grid is ruled", "Square-cut: 2px controls,
  4px max".
- **Closed token table** (type · color · radius · spacing · density · rules · motion). Values
  come from the Blueprint / the existing `@theme`; the file says what each is FOR.
- **NEVER list, 8–12 concrete, checkable items.** Concrete = a reviewer can point at a pixel
  or a line. Good: "never a corner >4px on cards/buttons/chips (exceptions: hero arch,
  portraits)". Bad: "never look generic". Declare exceptions inline so they are decisions,
  not violations.
- Lives next to PRODUCT.md/DESIGN.md (project-direction-protocol §2); it is the "forbidden"
  half of the direction file.

Starter NEVERs that caught real problems (adapt, don't paste):
Inter / a 4th typeface · purple-violet or any hue off the palette (stray glow colours count) ·
corners >4px, pills · floating filled cards for a list of options (use a ruled grid: shared
1px rules, no gutters) · decorative shadows / backdrop-blur panels · more than one glow per
screen · gradient text, accent as large fill · "BOLD CAPS + one *italic serif word*" headline
formula as section grammar · an uppercase mono eyebrow above every section (mono only for
data) · generic tech imagery / emoji icon tiles · motion that bounces or gates visibility ·
pure #000/#fff.

## 2. Critique before construction (two isolated passes)
Run both BEFORE editing, as separate agents that never see each other's output:
- **Blind Eye** — receives ONLY screenshots (1440 fold + mid section, 390 fold + mid, a
  downscaled full page) + the world sentence + NEVER list. No source, no rationale, no
  author. Returns per rule: VIOLATED / OK / UNASSESSABLE-FROM-PIXELS with image+element
  evidence, top 5 problems by visitor consequence, and what to preserve.
- **Code scan** — `impeccable` detector + grep per rule over the home's component tree and
  `globals.css`; output file:line + fix scope (token / global class / component).
Why both: the generic detector returned **0 findings** (it doesn't know project rules); the
blind pass caught visual tells (headline formula, glows) and the scan caught what pixels
hide (`backdrop-blur` on the nav, a shadow on the mobile menu, the radius tokens themselves).
Result: 7 of 12 broken, all found before a line changed.

## 3. Fix at the lowest level that removes the cause
Order: token → global class → component. On renanspatin.com, 10 files, +36/−65:
- `--radius-*` 8/12/16/24/pill → 2/4/4/4/4 (fixed most of rule 3 in one edit; note it
  ripples to every page using the tokens).
- `.display-grotesk` re-pointed to serif, sentence case → every section headline at once.
- Deleted `.glow-wash` and body radial glows; the hero keeps the only light.
- Components only where structure changed: floating cards → ruled grid
  (`ul: border-t border-l` + `li: border-r border-b`, no gap; a 1-item group goes full width,
  never a half-open cell).
"Moderate" means the fold should look almost the same; the change shows in the middle of the
page, where templates hide.

## 4. Verify and show it
- lint · typecheck · build · tests · mobile suite; screenshots 1440/390 same framing as before.
- Before/after video: `agentic-media-kit` → `scripts/capture-before-after.sh <before> <after>`
  (run the old build from a `git worktree --detach main` on another port) → `BeforeAfter-Landscape`.
  Captions per changed section, an audit checklist scene with the broken rules, and say
  "Untouched" where nothing changed — honesty reads as craft.

## Gotchas learned
- **Identity preservation beats reflex lists.** impeccable's reflex-reject list names
  Fraunces and Instrument Sans; they were already the brand, so they stayed. Reflex lists
  are for greenfield picks.
- **Tailwind v4:** `rounded-[--radius-lg]` (no `var()`) compiles to `border-radius:--radius-lg`
  — invalid, renders square. Always `rounded-[var(--radius-lg)]`.
- **Full-page captures:** sticky headers land mid-page (inject CSS to make them static),
  lazy images come out blank (set `loading="eager"` + scroll through first), and pages taller
  than ~16k px must be tiled for Chrome/Remotion (≤3000px JPG tiles).
- A post about the change must claim only what happened: if the reviewer was "inspired by"
  a tool rather than the tool itself, say so.
