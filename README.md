# premium-site-skill

Claude Code skill for building premium websites: sales LPs (GRIGOLETTO), identity sites
(landonorris.com / OFF+BRAND), immersive 3D heroes, all on the Design System Blueprint
tokens with a hard performance budget.

## Install
```bash
ln -s "$(pwd)/skill" ~/.claude/skills/premium-site
```

## Structure
- `skill/SKILL.md` — router + shared workflow + performance budget
- `skill/references/` — teardowns and playbooks (one file per dissected site/technique)
- `docs/` — working notes

## Evolving
Dissect a site → write `skill/references/<site-or-technique>.md` → link it in SKILL.md's
register table → commit. Keep each reference self-contained.
