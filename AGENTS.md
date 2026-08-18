# AGENTS.md

## Overview
A single-file vanilla HTML/CSS/JS game ("Pasa Pasa"): drag the raisin (🍇 pasa) to the house door to trigger a short dialogue with the wave (🌊 ola). All UI text and dialogue is in Spanish — keep new strings in Spanish.

## No build system
- `pasa_pasa.html` is the entire app: inline `<style>`, markup, and one inline `<script>`. No dependencies, no bundler, no npm, no server needed.
- To test: open `pasa_pasa.html` directly in a browser. There is no test suite or lint/typecheck step.
- Do not introduce a build tool, framework, or external assets unless asked — the file is intentionally self-contained.

## Code conventions (differ from defaults)
- Minified style: short variable names, no semicolons on single-line statements, minified CSS (no spaces/newlines between rules).
- All layout is absolute positioning inside `#game` (width `min(100vw,480px)`, height `100vh`). The raisin is dragged by setting `left`/`top` in px; movement is bounded by clamping inside `move()`.
- Input is mouse + touch: `mousedown`/`touchstart` on `#raisin`, move/up listeners on `window`. `touchmove` must stay `passive:false` so `preventDefault()` works.
- Game state lives in the global vars at the top of the script (`dragging`, `done`, `step`, `offsetX/Y`). The dialogue is a 3-step machine driven by `step` in the `#next` click handler (`arrive()` -> step 0 -> 1 -> 2 -> show restart).
- `#restart` reloads the page (`location.reload()`); there is no reset-state logic.

## Gotchas
- Element references are captured once at script start via `getElementById`; `#wave` and `#restart` start `display:none` and are revealed by JS.
- Dragging hit-testing compares the raisin's center against the door rect (with tolerance) inside `move()` — the game "wins" as soon as the center enters the door zone.