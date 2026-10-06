# Prompt Motion — animation reference library

[prompt-motion.com](https://prompt-motion.com/) (curated by @p4nthera_): ~230 motion videos
made with Claude Opus 5.5, each with the exact prompt or skill behind it and a link to the
creator's post. We use it ONLY as a reference for animation: watch the video, read the
prompt, borrow the technique. Videos and prompts belong to their creators — cite the entry
when porting a move, never ship one verbatim.

How to search it: the site sorts by popular and filters Prompt vs Skill. Each entry page has
the video, the full prompt (or the skill repo) and the original post. Pick 2–3 references by
the MOVE you need (morph, kinetic type, a style), not by product category.

## What the library teaches
- **One-liners vs specs.** Many top showreels are a single sentence ("make a 15-second motion
  graphics showreel that shows what an incredible motion designer you are... go all out") —
  Opus's taste does the work. Use that for exploration. For brand/product films, the winners
  are full specs (below). Explore loose, ship specific.
- **The 6-block spec** (e.g. "Launch video remake comparison", "Shape morphing through UI
  states"): `<inputs>` what to ask + defaults · `<direction>` canvas/fps/duration, feel,
  palette by ROLE, type, story, and a **Banned** line · `<structure>` per-shot frame ranges and
  keyframe values, cuts locked to the beat (BPM → frames/beat) · `<build>` deterministic
  `seek(t)` render, keyframe tables, seeded randomness, per-frame screenshots ·
  `<gotchas>` · `<start>` ask inputs, then show 4 named stills before the full render.
- **Banned lines are NEVER lists for motion**: "bouncy easing, particle bursts, glow",
  "crossfades, frozen frames, stock UI kits, glows on UI text, anything that looks like a
  template", "no beige 'artsy' look, no eyebrow labels", "no generic push/slide/rotate-swing",
  "no animator jargon on screen". Same discipline as `design-world-never-protocol.md`.
- **Recurring craft rules worth stealing**:
  - One element, never cut: the same shape morphs size/radius/colour while its content
    swaps behind a short blur; a cursor drives each change; last frame = first frame (loop).
  - Springs with at most a tiny overshoot; exponential ease-out entrances; leave on an
    accelerating move or a blur ramp; blur follows speed and direction (directional smear).
  - One primary move per transition; a new idea every 1.5–2 s; a sound on every hit.
  - Set every from-state at t=0 (seek-safe); never cover an exit.
  - Copy rules inside the prompt: no invented results/figures, name what the product does,
    not the abstraction.
- **Stack note**: several entries use HyperFrames (HTML+GSAP → video, heygen) instead of
  Remotion; the deterministic per-frame contract is the same. Ours stays Remotion
  (`agentic-media-kit` → capture · compose · publish). Mapping: keyframe tables →
  `interpolate`, seeded random → `random(seed)`, stills checkpoint →
  `npx remotion still <id> --frame=N` and LOOK before `render`.

## Catalog (curated by technique, popular-first as of 2026-10-06)

### UI / product motion (states, cursor, morphs)

- [Shape morphing through UI states](https://prompt-motion.com/twoclipping-5cba86) · @twoclipping
- [Morphing UI states loop](https://prompt-motion.com/demonugc-4c5753) · @demonugc
- [Launch video remake comparison](https://prompt-motion.com/notdwd-c2037d) · @notdwd
- [Frame by Frame launch video](https://prompt-motion.com/notdwd-7de38a) · @notdwd
- [Badge unlock screen animation](https://prompt-motion.com/bthreeagency-b9b8d9) · @BThreeAgency
- [Photo print app launch film](https://prompt-motion.com/twoclipping-221cab) · @twoclipping
- [Spotify product film](https://prompt-motion.com/brainextends-e19ac3) · @brainextends
- [Dub promo video](https://prompt-motion.com/steventey-0d20e4) · @steventey
- [TypingMind logo reveal](https://prompt-motion.com/tdinh-me-815acb) · @tdinh_me

### Launch & product films (SaaS promos)

- [Reddit marketing tool launch video](https://prompt-motion.com/anthonyriera-9b1b2a) · @anthonyriera
- [Lightspark social promo video](https://prompt-motion.com/davidmarcus-a28a60) · @davidmarcus
- [SuperX feature release teaser](https://prompt-motion.com/robj3d3-b18fad) · @robj3d3
- [mdfor.dev product intro video](https://prompt-motion.com/ho-ba-f3f0e9) · @HO_BA
- [founder.page launch video](https://prompt-motion.com/ratheejaisal-1f4538) · @RatheeJaisal
- [Supademo launch video](https://prompt-motion.com/jhylee95-e1152f) · @jhylee95
- [Ren app launch video](https://prompt-motion.com/macrohou-33959d) · @macrohou
- [Paper-style product launch film](https://prompt-motion.com/ik-builds-b8bdcf) · @ik_builds
- [Supasito launch video](https://prompt-motion.com/wozu-co-242285) · @wozu_co
- [Database client launch video](https://prompt-motion.com/iammxfschr-d68f90) · @iamMXFSCHR

### Kinetic typography

- [Bold kinetic type showreel](https://prompt-motion.com/shneural-2abdfa) · @shneural
- [Kinetic type showreel](https://prompt-motion.com/zheke-38deff) · @zheke
- [Kinetic type motion showreel](https://prompt-motion.com/topi-003-9ca6b5) · @_topi_003
- [Bold typographic motion showreel](https://prompt-motion.com/motiondsgnr-9dcfa6) · @motiondsgnr
- [Type, form, space showreel](https://prompt-motion.com/web3wesley-7bb108) · @Web3Wesley
- [Kinetic type and particle showreel](https://prompt-motion.com/m1n9-k7-ff9ab9) · @m1n9_k7
- [Kinetic type showreel](https://prompt-motion.com/itsuki-dev-10942d) · @itsuki_dev
- [Kinetic type self-portrait](https://prompt-motion.com/souravbhar871-61f424) · @souravbhar871

### Easing, morphing & motion-principles showreels

- [Easing and morphing showreel](https://prompt-motion.com/roundtablespace-d3a1be) · @RoundtableSpace
- [Motion techniques showreel](https://prompt-motion.com/lukasersil-0ed38e) · @lukasersil
- [Motion principles showreel](https://prompt-motion.com/chatgptastra-0d95c7) · @ChatGptAstra
- [Motion design principles reel](https://prompt-motion.com/tolgayhickiran-e90799) · @tolgayhickiran
- [Keyframe motion design reel](https://prompt-motion.com/grace-sunnyy-29bc61) · @Grace_sunnyy
- [Kinetic shapes showreel](https://prompt-motion.com/shinskinakamoto-20190b) · @Shinskinakamoto
- [Shape and dot motion showreel](https://prompt-motion.com/umangratani-57f86a) · @umangratani
- [Geometric motion designer showreel](https://prompt-motion.com/leonkohli-6dc01b) · @LeonKohli

### Distinct visual styles (art direction to borrow)

- [Engraving-style Claude ad](https://prompt-motion.com/lexnlin-038035) · @LexnLin
- [Swiss-style motion showreel](https://prompt-motion.com/levabashidze-2d713a) · @levabashidze
- [Blue editorial motion showreel](https://prompt-motion.com/fionntobin-97b7af) · @fionntobin
- [Risograph studio intro](https://prompt-motion.com/rneayan-474bcf) · @rneayan
- [Oscilloscope-style studio motion graphic](https://prompt-motion.com/rneayan-ea6129) · @rneayan
- [Clay-style music video](https://prompt-motion.com/davidshulmanfl-371638) · @DavidShulmanFL
- [Shadow theatre home story](https://prompt-motion.com/x4b47x-9cc84f) · @x4b47x
- [Sketchbook animals come alive](https://prompt-motion.com/abderrahmen-g-9e8ed0) · @abderrahmen_g
- [Poster breaking its frame](https://prompt-motion.com/pankajkumar-dev-0bee87) · @pankajkumar_dev
- [Deliberately terrible motion showreel](https://prompt-motion.com/henkpoley-3d3407) · @HenkPoley

### Explainers, data & timelines

- [Recursion explained in genres](https://prompt-motion.com/emollick-8661a8) · @emollick
- [Animated business explainer](https://prompt-motion.com/alex-prompter-1ea044) · @alex_prompter
- [Derivative concept explainer](https://prompt-motion.com/linearuncle-5d2bae) · @LinearUncle
- [History of AI timeline](https://prompt-motion.com/kloss-xyz-fe0c31) · @kloss_xyz
- [What is Git explainer](https://prompt-motion.com/yunn260414-60d996) · @Yunn260414
- [Moroccan political history timeline](https://prompt-motion.com/triglyceee-bd5b08) · @TrigLyceee
- [Pixel art LLM history](https://prompt-motion.com/pluginplay-3d4006) · @PluginPlay__
- [First & 10 yellow line explainer](https://prompt-motion.com/blueoctopusai-dd17bd) · @blueoctopusai
- [Photon journey from the Sun](https://prompt-motion.com/apoorvjain25-d24184) · @apoorvjain25
- [What happens in one second](https://prompt-motion.com/nolkeeg-bf062e) · @nolkeeg
- [Token bucket rate limiter](https://prompt-motion.com/parkerrex-1a54fe) · @ParkerRex

### 3D, particles & generative

- [Particle sphere motion reel](https://prompt-motion.com/blue-clarity-ef56d0) · @blue_clarity
- [AI data centre 3D tour](https://prompt-motion.com/sayan-shanky-f850d8) · @Sayan_shanky
- [AI data centre 3D film](https://prompt-motion.com/mdaman010-e7226a) · @mdaman010
- [Three.js Skyraider aircraft model](https://prompt-motion.com/buildfastwithai-ed9447) · @BuildFastWithAI
- [Beat-synced bouncing 3D ball](https://prompt-motion.com/gorden-sun-041f56) · @Gorden_Sun
- [Ball escaping twelve rings](https://prompt-motion.com/kantamk-61f410) · @kantamk
- [Collision-driven music machine](https://prompt-motion.com/kamstudiolabs-447565) · @KamStudioLabs
- [UK dubstep track with ASCII visuals](https://prompt-motion.com/rames-jusso-589f80) · @Rames_Jusso

### Scroll / web sections

- [Five scroll-based sections](https://prompt-motion.com/ercankeskinx-ec2568) · @ercankeskinx
- [Developer portfolio site showreel](https://prompt-motion.com/wani-shola-16ca7a) · @wani_shola
- [Studio website showreel](https://prompt-motion.com/andginja-333d32) · @andginja

### Skills (repos to study)

- [Cinetic skill launch film](https://prompt-motion.com/lexnlin-6161a6) · @LexnLin
- [Reddit marketing tool launch video](https://prompt-motion.com/anthonyriera-9b1b2a) · @anthonyriera
- [Animated agent session story](https://prompt-motion.com/jake11moran-a269c4) · @jake11moran
- [Indian civilisation history film](https://prompt-motion.com/buildfastwithai-53234e) · @BuildFastWithAI

Skill repos behind those entries: [cinetic](https://github.com/Leonxlnx/cinetic) ·
[product-film-skill](https://github.com/Rieranthony/product-film-skill) ·
[hyperframes session-story](https://github.com/heygen-com/hyperframes-community-skills/tree/master/skills/session-story) ·
[generative-film-skill](https://github.com/buildfastwithai/buildfast-skills/tree/main/generative-film-skill).
Read a skill before reinventing a film pipeline; adopt pieces, re-skin to our tokens.

## Using it on a site (not just videos)
**The bridge: `seek(t)` → `seek(scroll)`.** The best prompts draw every frame as a pure
function of time. On a site, feed that same function with scroll progress
(`useScroll({ target, offset })` → `useTransform(progress, v => sample(v).x)`): the film
plays forward and backward with the reader, no video file, no new library.
Recipe (renanspatin.com, "Como posso ajudar" diagram, 2026-10-06):
- One element never cut (from "Shape morphing through UI states"): a brass point carries a
  request down UI → API → domain → data and the response climbs a brass rail back.
- Keyframe table `[progress, x, y]` with holds at each stop, eased per segment (from
  "Launch video remake comparison"); the point stretches along its direction of travel
  (`scaleX/scaleY = 1 + k·sin(πt)`, transform only — never width/height).
- Each layer's stroke goes accent while the request is inside; a mono log line writes in
  with a short blur and STAYS (ledger), so the end state explains itself.
- Server, reduced motion and print render the finished state (all logs visible): use a
  constant `useMotionValue(1)` until hydrated or when `useReducedMotion()`.
- **Scroll or loop?** Scroll-driven felt like work to the reader on the real site; the
  shipped version loops on time instead: `animate(cycle, 1, { duration: 7, ease: "linear",
  repeat: Infinity })` started/stopped by `useInView`, sequence on 0–0.8 of the cycle, the
  finished ledger holds, then a fade so every cycle starts empty (last frame = first frame).
  Keep scroll-driven for storytelling sections where the reader is the clock.
- Scroll "feels dense/heavy" is usually inertia + content lag, not fps (headless at 4x CPU
  throttle measured 60 fps either way): Lenis `lerp` 0.1 → 0.16 and scroll-scrubbed entrance
  travel 48 → 24 px fixed it.
- Sticky panels don't move relative to the viewport — drive them from the scrolling
  sibling (the list/grid), not from the panel itself. Tune `offset` until the sequence
  finishes while the drawing is fully on screen (desktop `["start 60%","start 5%"]`,
  mobile `["start 80%","end 70%"]`). Verify with a stepped-scroll still strip and a
  screencast.
The moves port to web motion (motion/react, CSS): UI-state morph → shared-layout
transitions (`layoutId`); kinetic type → SplitText reveals ordered by hierarchy; directional
blur on fast moves → `filter: blur()` keyed to velocity, only on transform-driven elements.
Still obey Guide · Connect · Respond (SKILL.md) and the performance budget.
