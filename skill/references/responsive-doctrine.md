# Responsive doctrine — mobile is the site, not a variant

Born from a real failure (2026-08-31): profissional_site shipped headings styled
`display ... sm:text-[4rem]` with **no base size** — flawless at 1440px, 16px dust at 390px.
Nobody noticed because verification only looked at desktop. Never again.

## Hard rules

1. **Every `sm:`/`md:`/`lg:` styling utility needs its base pair.** `sm:text-[4rem]` without a
   base `text-*` is a bug, not a default. Same for spacing (`sm:mt-24` → pair with `mt-12`),
   grids, and widths. Grep before shipping:
   `grep -rn 'sm:text-\[' src | grep -v 'text-\[|text-(xs|sm|base|lg|xl|[2-9]xl)'` (adapt).
2. **Design the 390px composition on purpose.** Not "whatever stacks": decide what the first
   mobile viewport shows (hero art visible? which plates/cards survive? scrim strength?).
   A premium desktop scene that becomes a black rectangle on mobile is a failed section.
3. **Type scale**: mobile display ≥ 2.25rem for section titles, ≥ 3rem for hero numbers/stats;
   use `clamp(min, vw, max)` where the min is chosen BY LOOKING at 390px, never by formula.
4. **Interactions degrade by intent**: pointer-tilt/hover effects OFF on touch, but replace
   them with something (scroll parallax, drawn strokes) — don't just delete the life.
5. **Weight**: mobile gets smaller media via `sizes`/`srcset`; blur ≤ 8px; shaders/canvases
   skip on `pointer: coarse` with the static image standing in.
6. **Thumb ergonomics**: hit areas ≥ 44px, primary CTA reachable, no hover-only affordances.

## Mandatory verification loop (every visual change, no exceptions)
Screenshot and LOOK at, minimum: **390×844, 768×1024, 1440×900** — and for the changed
section, scrolled INTO view (reveals fire on scroll; a top-of-page screenshot lies).
For full-page audits: step-scroll captures every ~800px at 390 (`scrollTo` + shot), because
the worst bugs live mid-page. Also check `document.documentElement.scrollWidth <= innerWidth`
(no horizontal overflow) and run Lighthouse mobile, not just desktop.

A change is not "done" until the 390px screenshots have been looked at. Desktop-only
verification is the single most common way premium sites rot.

## Failure patterns caught in the wild (add yours here)

- **`sm:`-only sizing** → 16px headings on phones (the founding bug).
- **Absolute overlays colliding at other widths.** Decorative plates/cards positioned in %
  inside a hero looked fine at 1440, sat ON TOP of the proof text at 390. Any absolutely
  positioned decoration must be re-checked at every breakpoint against the CONTENT flow —
  or hidden below `sm:` and replaced by a simpler arrangement.
- **Theme-flip sections that don't flip every component.** A `.paper` (light) section
  overrode text colors but not `.btn-secondary` — parchment text on parchment bg =
  invisible button. When a section flips palette, audit EVERY component that can appear
  inside it (buttons, chips, borders, focus rings), not just text utilities.
- **Unlayered CSS silently beats Tailwind v4 utilities.** A plain `.paper > * { position:
  relative }` outranks the `@layer utilities` `.absolute` class at equal specificity —
  the element computed `position: relative` with zero warnings. When a utility "doesn't
  apply", check for unlayered rules hitting the same element; restate the property in
  your own (unlayered) class.
- **Pointer-only life = dead on touch.** Every pointermove-driven effect needs a touch
  counterpart: autonomous drift (slow sin/cos sweep of the same motion values) and/or
  scroll-driven offsets on `pointer: coarse`. Interactivity is an input-agnostic budget.
