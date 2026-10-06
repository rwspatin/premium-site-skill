# Prompt Motion — motion-video prompts made with Opus 5.5

[prompt-motion.com](https://prompt-motion.com/) (curated by @p4nthera_): ~230 motion videos
made with Claude Opus 5.5, each with the exact prompt or skill behind it and a link to the
creator's post. Filter "Prompt" vs "Skill"; sort by popular. Videos and prompts belong to
their creators — study and cite, never ship one verbatim.

Use it as (a) the fidelity bar for motion/promo prompts, next to `prompt-library/` (which is
the bar for single-file sites), and (b) a reference hunt before a Remotion/HTML film:
search a genre ("launch film", "kinetic type", "explainer", "showreel", "remake comparison").

## Anatomy of the strongest prompts (e.g. "Launch video remake comparison", @notdwd)
Tagged blocks, in this order:
1. `<inputs>` — what to ask the user for (name, logo, colours, copy, track) + defaults if skipped.
2. `<direction>` — canvas/fps/duration, feel in one line, palette by ROLE, type, the story
   beat by beat, and a **Banned** line ("crossfades, frozen frames, stock UI kits, glows on UI
   text, Math.random, anything that looks like a template"). Same idea as our NEVER list
   (`design-world-never-protocol.md`), applied to motion.
3. `<structure>` — per shot, frame ranges and keyframe values in stage px
   ("y932 f0 → 776 f2 → 683 f5 …"), cuts locked to the beat grid (BPM → frames per beat).
4. `<build>` — rendering contract: one page drawn by `seek(t)` as a pure function of the
   frame; keyframe tables + interpolation (continuous values, no `Math.floor` on motion);
   seeded hashes instead of `Math.random`; directional blur via SVG feGaussianBlur x/y;
   frosted glass = blurred, lightened copy of what's behind clipped to the shape; render one
   screenshot per frame after fonts load; encode H.264 yuv420p.
5. `<gotchas>` — measure text after fonts load, long names must scale the lockup, white on
   white needs a 1px edge.
6. `<start>` — ask for inputs, then show **4 named stills** at specific frames before the
   full render.

The short creative prompts that also rank (e.g. "Engraving-style Claude ad", @LexnLin) win
on taste rules instead of numbers: "no beige 'artsy' look, no eyebrow labels", "borrow
techniques but change the design so it looks like your own taste", "every morph has to be
perfect" — the same tells our NEVER lists ban on sites.

## How we apply it
- Remotion is already deterministic per frame (`useCurrentFrame`), so the `<build>` contract
  maps 1:1: keyframe tables → `interpolate`, seeded randomness → `random(seed)`, stills
  checkpoint → `npx remotion still <id> --frame=N` and LOOK before `render`.
- Write promo prompts in the 6-block shape; keep the Banned line; checkpoint stills.
- Pipeline for our own films: `agentic-media-kit` (capture → compose → publish).
- Opportunity: the gallery takes submissions ("Submit") — a finished piece with its prompt
  can be listed there (ask the user before submitting anything).
