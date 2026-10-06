# Prompt library — complete single-file recreation prompts (Altra Labs bonus pack)

Five battle-tested, hyper-detailed prompts (exact values, GLSL verbatim) that each produce a
premium single `index.html` — plus a short preview video of the expected result. Use them
two ways: (a) run as-is to study/scaffold the technique; (b) mine them as the GOLD STANDARD
of prompt specificity — this is what "context·stack·structure·behavior·constraints" looks
like at maximum fidelity (see ai-prompting-protocol.md).

| Prompt | Register / look | Signature techniques it teaches |
|---|---|---|
| `fromzero.md` (30 KB) | Dark creative-studio one-viewport hero | Three.js night valley; video panel tilting to cursor; **real-time analytic reflection on a black lake** + cursor ripples; floating live-code planes; preloader curtain 00→100; scramble-in headline |
| `forma.md` (26 KB) | Light violet studio contact page | **Raw WebGL 1, zero libs**; inline spring engine; preloader with giant spring counter + gradient bar that collapses into its edge; choreographed cascade assembly |
| `stride.md` (52 KB) | Deep-blue fintech landing | Three.js **plasma burst** (filaments + sparks + bloom); hero that **shrinks into a rounded inset card on scroll** while the burst spins/zooms; counter-directional scroll-linked logo marquees |
| `loopstack.md` (27 KB) | Black "footer hero" single screen | Video fill w/ radial fade into page; pinned giant wordmark; pulsing status dot; footer-as-hero composition |
| `baseline.md` (39 KB) | Editorial navy tennis club (Lenis only) | Word-by-word clip-mask headline reveal; ghost words behind carousel; tilted photo cards; plain-JS rAF spring helper (no framework) |

For motion/promo FILMS (not sites), the equivalent bar is `../prompt-motion-gallery.md`.

Rules of use:
- These are STUDY/SCAFFOLD material - shipping one as-is for a client violates the
  build-plan protocol (every brand demands its own solution). Re-skin to Blueprint tokens.
- The techniques are extractable individually (e.g. stride's hero-into-card scroll move,
  fromzero's analytic reflection) - cite the file when porting one.
- Preview videos (`*-preview.mp4`, 640px) show the expected end state - watch before running.

## Benchmark run (2026-09-06)
`stride.md` executed end-to-end by the skill pipeline → `bench/stride/index.html` (58 KB,
single file). Result vs the reference video: **high fidelity** — preloader (counter + gradient
bar grow-and-slide), plasma burst (60 filaments + 700 sparks + core + bloom, cursor magnet),
hero shrink-into-card, alpha-ramped per-letter headings, scroll-linked marquees, About
chips timeline, stats bento, showcase hover columns, 400vh pinned portfolio cylinder
(idx/name meta live), chain/heart chrome GLBs (Draco) over seeded mesh gradients, layered
pinned footer reveal, contact form dots. Known deltas: burst slightly denser/brighter than
the reference; faint grey rectangle behind the core over the gradient (core-sprite plane +
bloom under mix-blend lighten — fixable by fading the halo to zero before the plane edge).
Verdict: the 5-part max-fidelity prompt format reproduces near-1:1; this is the fidelity bar.
