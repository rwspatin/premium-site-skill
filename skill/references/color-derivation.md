# Colour derivation — filling the accent slot (sample → converge → justify)

**The Blueprint stays the token source.** Canvas, ink, grey ramp, hairlines, shadows, radii,
type, motion: all from `design-system-blueprint.md`, unchanged. The Blueprint has exactly ONE
product-specific slot — `--accent` (with hover, pressed, glow, focus ring derived from it). This
protocol fills that slot and nothing else. It never adds a second accent or a surface colour; a
brand that already owns a fuller palette is an identity decision, declared in `DESIGN_WORLD.md`
(design-world-never-protocol §1), not derived here.

Why: an accent picked from the model's prior is a lottery with three tickets (SaaS blue, violet,
terracotta) — every project ends up the same colour. An accent sampled from the subject carries
its own "why", and that sentence is the anti-slop check.

## Reference values (Blueprint default, in OKLCH)
| Token | Hex | OKLCH |
|---|---|---|
| `--accent` | #2F6FED | 0.574 0.202 262 |
| hover | #4C82FF | 0.636 0.195 264 |
| pressed | #1E5AD6 | 0.509 0.200 262 |
| paper / ink / surface-dark | #FAFAF8 / #16181D / #0E0F12 | 0.985 · 0.209 · 0.169 (C ≈ 0) |
#2F6FED is a placeholder: it sits in the model-default blue band. Keep it only if Step 3 can
justify it for THIS product.

## 1. Sample (3–6 candidates, in OKLCH, never invented)
Priority order:
1. **Brand assets** — logo SVG, existing VI, the live site's CSS:
   `grep -hoE '#[0-9A-Fa-f]{6}' brand/*.{svg,css,html} | sort | uniq -c | sort -rn | head`,
   drop near-greys (C < 0.03). Product screenshots often show a CUSTOMER's demo brand: when two
   strong hues appear, decide which one is actually the product's.
2. **Real content imagery** — the dominant non-neutral hue in the product photos, the material,
   the place (eyedrop 3 spots per photo, convert, note L/C/H).
3. **Material / cultural context** — what the subject is made of and where it comes from. Same
   hue, different coordinates: lacquer vermilion (low L, muted C) vs cola red; indigo dye vs SaaS
   blue; moss/matcha vs terminal neon; ochre/mustard or ipê yellow vs safety yellow; terra-roxa
   brown vs chocolate. Chroma decides whether it reads crafted or plastic.
Write each candidate as `oklch(L C H) — source`.

## 2. Converge (one accent, Blueprint-compatible)
- **Pick one hue** from the candidates; keep the others only as notes in DESIGN_WORLD (photos and
  content may carry them; tokens do not).
- **Chroma band**: 0.12–0.20 (accent lives on small areas only — links, primary CTA arrow, focus
  ring, per Blueprint §1). Lower (0.08–0.12) reads printed/ink and is often the premium choice;
  > 0.25 is screen-neon, allowed only when "electronic" IS the world.
- **Lightness from contrast, not taste**: accent text on paper must reach 4.5:1 (on #FAFAF8 that
  lands near L ≤ 0.58 — verify, L is a guide); focus ring 3:1 on every surface it touches. If the
  site has dark sections, derive `--accent-on-dark` (same H/C, L raised until 4.5:1 on #0E0F12,
  typically ≥ 0.68).
- **Variants by L only, same H and C** (the Blueprint's own steps): hover L + 0.06, pressed
  L − 0.065, glow = accent at α 0.30 (`--shadow-glow`), focus ring = accent or accent-on-dark.
- **Gamut**: if the chosen L/C falls outside sRGB, lower C (never shift H) until it fits.
- **Grayscale test** still passes: hierarchy must survive with the accent desaturated.
- **Reject bands** unless brand-owned and stated: H 255–265 with C > 0.18 (default SaaS blue),
  H 280–320 (violet — F9 in `gates-floor-reflex-finish.md`), H 15–55 terracotta on a warm serif
  page (the most recognised generated look).

## 3. Justify (one sentence or back to Step 1)
Write it in the DESIGN_WORLD token table, next to the value:
> `--accent: oklch(0.52 0.09 185)` — verdigris sampled from the workshop's copper fittings;
> chroma held at 0.09 so it reads as patina, not plastic.
Cannot write the sentence = you picked from the prior. Go back and sample.

## Output
```css
:root{
  --accent: oklch(0.52 0.09 185);          /* why: see DESIGN_WORLD.md */
  --accent-hover: oklch(0.58 0.09 185);
  --accent-pressed: oklch(0.455 0.09 185);
  --accent-on-dark: oklch(0.72 0.09 185);  /* only if dark sections exist */
  --shadow-glow: 0 0 30px oklch(0.52 0.09 185 / .30);
}
```
Everything else in `:root` is the Blueprint block verbatim. Verify every accent pair (link on
paper, CTA arrow on ink, focus ring on paper and dark, accent-on-dark text) with a contrast
checker before claiming done.
