# Motion teardown protocol (Altra Labs M2-A4)

How to dissect ANY reference site's motion so it stops looking like magic and starts looking
built. Use this before porting any aesthetic; output feeds a reference file in this repo.

## The 5 analysis axes
1. **Sequence** — what enters first, second, third? The reveal order encodes hierarchy.
   Watch the entrance WITHOUT scrolling first; note the order and the delays between entries.
2. **Trigger** — what starts each movement: page load · section entering viewport ·
   scroll position (scrubbed) · user input (hover/click/focus). **Scroll-linked vs
   fires-on-enter changes the whole implementation** — always classify this first.
3. **Timing** — duration, delay, stagger interval. Fast/slow/deliberate? Feedback is short;
   impact moments are slower.
4. **Easing** — does it accelerate, decelerate, or run constant? (Exact curve optional —
   the behavior class is what you reproduce.)
5. **Repetition** — does the pattern recur across sections/lists? Recurring pattern =
   the site's motion SYSTEM (the real secret); one-offs = moments.

## The 5-question card (write one per movement, in plain sentences)
> what moves · how it starts · where it comes from · how long it takes · how it ends

Plain-sentence notation, e.g.: "headline rises+fades 0.8s after plate", "image scales 1.0→1.15
tied to scroll", "cards enter with 0.05s stagger", "background cross-fades between sections".
This translation from visual to sentence is what makes it reproducible (and promptable).

## Flow
Don't only analyze movements in isolation: check how the END of one prepares the START of the
next. Gradual background color/scale/position changes that bridge sections = flow. Cutting
between backgrounds is the amateur tell.

## Atmospheric motion
Slow floats/scale drifts that answer to nobody are allowed but must stay secondary; if they
compete with content they're too strong.

## Goal
Extract the LANGUAGE, not the animations: e.g. "soft fades + short slides + consistent stagger
+ big scroll-tied transitions". With that map you recreate the feeling on your own design.

---

## Worked example: neverland.agency (analyzed 2026-09-02)

Stack: Vue + Pixi (3 canvases), GSAP ScrollTrigger in page chunks. Awwwards SOTD.

- **Sequence (entrance):** 3D arch scene settles first → serif headline "Welcome to
  Neverland" → sub → "Scroll to Discover" hint. Entrance timeline durations 3–4 s (long,
  anticipation-building — brand permission).
- **Trigger (the hero trick):** the camera "walking through the arch" is a **pre-rendered 3D
  image sequence (~1600 webp/jpg frames) scrubbed by scroll onto a canvas** (`scrub: true` /
  `scrub: 0.5`, `drawImage`, `requestVideoFrameCallback`) with heavy use of pinned sections
  (85 `pin` refs). User controls the rhythm; scroll back = animation returns. This is Tier 0.5
  between our video-hero and WebGL: cinema-quality 3D with zero runtime 3D cost.
  Cost: asset weight — mitigate with webp, tiny dimensions, lazy ranges.
- **Timing/Easing:** few explicit durations (scroll drives most); `power1.inOut` for the rest.
- **Flow:** background cross-fades pink → cream between scenes; the object of one scene
  persists into the next framing (end prepares start).
- **Repetition:** every content section repeats slide+fade+small-stagger; the scrubbed scene
  is the recurring spine of the page.
- **Language extracted:** one scrubbed cinematic spine + long welcome + quiet repeated
  reveals + color-bridged sections.
