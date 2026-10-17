# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

@AGENTS.md

AGENTS.md (imported above) is the main guide: single self-contained `index.html`, no build, hybrid canvas + DOM architecture, code conventions, gotchas. The notes below cover what it leaves out.

## Running
There is no build, install, lint or test step. Open the file in a browser:

```bash
start index.html
```

(`open index.html` on macOS.) For the desktop app's browser pane, `.claude/launch.json` defines `pasa-pasa`, a static server (`python -m http.server 8765`). Check changes by hand: mouse drag, a quick throw (inertia), touch (mobile emulation), and keyboard arrows after focusing the canvas.

## Details AGENTS.md doesn't spell out
- The dialogue is a Spanish pun: Pasa (raisin, and also "come in") says "¡Ola, ola!" (wave / "hola"), then "¿Puedo pasar?", and Ola replies "¡Pasa, pasa!". Keep the wordplay when editing lines. `say(who, line)` sets speaker and text, makes that character's mouth move, and re-pops the bubble.
- The house stands on a small sand island at the horizon, so the door sits where the path meets the sea; the raisin shrinks with `scaleAt()` as it walks up, and fits through the door at the end.
- Corner HUD: `#soundToggle` top-left, then `#counter`; `#githubLink` top-right (links to the GitHub repo and plays `sfxClick`).

## Docs and git
- `README.md` (Spanish) documents features, controls, sound and accessibility, and shows `docs/screenshot.png`. Update it when you make user-visible changes.
- Commits mostly use conventional prefixes (`feat:`, `fix:`, `refactor:`, `docs:`). The main branch is `main`.
