# Motion page contract — seekable, loopable, diffable

Agents animate blind: a screenshot of a RUNNING animation is a random frame. Any section whose
motion we must verify (a hero film, a looping diagram, a recreation from a reference video)
exposes one pure clock, so every still, frame diff and before/after video is reproducible. This
is the verification half of `motion-teardown-protocol.md` (which says WHAT moves) and the same
`seek(t)` that `prompt-motion-gallery.md` § "The bridge" feeds with scroll progress.

## 1. The contract
```js
window.anim = { duration, seek(t), play(from = 0), pause() }   // seek(t): pure function of t
```
- `seek(t)` yields the same image whatever was seeked before (frame 200, then frame 3, works).
- `?t=6.5` freezes at 6.5 s · `?external=1` never autoplays (a script drives it) · no param =
  plays and loops normally.
- `<html data-ready="1">` only after `document.fonts.ready` AND frame 0 is drawn.
- One clock: a paused GSAP timeline (`tl.time(t, true)` + `render(t)` for procedural parts) or
  one `motionValue` driving every `useTransform`. No rAF loops, `setTimeout` choreography, CSS
  keyframes or `Date.now()` driving visuals; randomness seeded and precomputed.
- Scroll-driven sections: `seek(p)` with `p` = scroll progress 0–1 (`duration = 1`) — the same
  function, so a scrubbed section is captured exactly like a film.
- Reduced motion, SSR and print render `seek(duration)` (the finished, self-explaining state).
- Ship it: the object is tiny and inert; `?t` parsing may be dev-only if the brief demands.

## 2. Capture deterministically
```bash
agent-browser open "http://localhost:3000/?t=2.5&external=1"
agent-browser wait --fn "document.documentElement.dataset.ready === '1'"
agent-browser screenshot out/t002.5.png
```
Never screen-record real time for proof (headless drops/duplicates frames): loop `seek(i/fps)`
→ screenshot → `ffmpeg -framerate 30 -i f%04d.png -c:v libx264 -pix_fmt yuv420p proof.mp4`.
For marketing pieces this feeds `agentic-media-kit` / Remotion stills the same way.

## 3. Seamless loop math (`frame(duration) == frame(0)`)
- **Define the opening state once** (`const OPEN = {...}`); the last segment tweens TO it. Two
  hand-copied numbers drift 2px and the wrap jumps.
- **Rotations**: tween to 360, then `set` 0 at the end — visually identical, numerically exact.
- **Drifts** (marquees, clouds, patterns): distance over the whole duration = an exact multiple
  of the pattern period, so `pos(duration) = pos(0) + k·period`.
- **Periodic cycles** (pulse, blink, wobble): period must divide the duration (12 s → 0.5, 0.75,
  1, 1.5, 2, 3, 4, 6 s; never 0.7) — or fade the amplitude to 0 before the wrap.
- **Particles** alive at the end are the ones alive at the start (or none at both).
- Return beat: spend the last 0.6–1.2 s returning with a move the piece already uses (morph back,
  iris, settle), then hold OPEN for a few frames — the hold hides the seam.
- **Check**: mean diff `frame(duration)` vs `frame(0)` < 1; `frame(duration − 1/fps)` vs
  `frame(0)` < 8 (0–255 scale, method below). Then watch three wraps live.

## 4. Frame-diff scoring vs a source video
Final step of a teardown recreation: prove each moment against the reference, not memory.
1. **Source frames**: `ffmpeg -ss <t> -i ref.mp4 -frames:v 1 -vf scale=192:108 src_<t>.png`.
   Does the source loop? first vs last frame diff < ~6 → reproduce it; else plan a return (§3).
2. **Page frames** at the same t via `?t=` (§2), same aspect, downscaled to 192×108.
3. **Score**: mean absolute RGB difference per pair —
   `magick compare -metric MAE src.png code.png null: 2>&1` (normalised value × 255).
4. **Read**: < 10 on holds is faithful; 10–30 during fast motion is normal (the reference has
   blur/noise); > 40 = wrong shape, colour or beat. The number RANKS moments; the side-by-side
   pair (`magick src.png code.png +append pair_<t>.png`) decides. A 3px offset scores low and
   still looks wrong; a correct beat two frames late scores high.
5. **Cadence**: every 0.5 s first; fix structure (shape, place, beat) before detail; then every
   `1/fps` around each transition and the loop seam — timing and easing errors live there.
Report with numbers: times checked, the 5 worst and why they remain, seam scores, and an honest
list of what differs from the source — never "pixel perfect".
