# AGENTS.md

## Overview
A single-file vanilla HTML/CSS/JS game ("Pasa Pasa"): drag the raisin (🍇 pasa) to the house door to trigger a short dialogue with the wave (🌊 ola). All UI text and dialogue is in Spanish — keep new strings in Spanish.

## No build system
- `pasa_pasa.html` is the entire app: inline `<style>`, markup, and one inline `<script>`. No dependencies, no bundler, no npm, no server needed.
- To test: open `pasa_pasa.html` directly in a browser. There is no test suite or lint/typecheck step.
- Do not introduce a build tool, framework, or external assets unless asked — the file is intentionally self-contained. Sounds use the Web Audio API (no audio files).

## Code conventions (differ from defaults)
- Short variable names, `'use strict'` inside a single IIFE `(() => { ... })()`. Keep everything scoped to that IIFE; no globals.
- CSS uses `:root` custom properties (`--sky-top`, `--sand`, `--purple`, …) and section comments (`/* ---------- House ---------- */`). The CSS is no longer minified; keep it readable.
- All layout is absolute positioning inside `#game` (width `min(100vw,480px)`, height `100vh`). The raisin wrapper is `#raisinWrap`; it is moved by setting `left`/`top` in px and bounded by clamping inside `clampToStage()`.
- Input is unified pointer events: `pointerdown` on `#raisinWrap` (uses `setPointerCapture`), `pointermove`/`pointerup`/`pointercancel` on `window` (`pointermove` is `passive:false`). `touch-action:none` on `#game`/`#raisinWrap` makes it work for touch. Keyboard arrows are also supported (`keydown` on `#raisinWrap`, 14px steps).
- Game state lives in vars at the top of the IIFE (`dragging`, `done`, `step`, `offsetX/Y`, `soundOn`, `audioCtx`). The dialogue is a 3-step machine driven by `step` in the `#next` click handler (`arrive()` -> step 0 -> 1 -> 2 -> show restart).
- `#restart` reloads the page (`location.reload()`); there is no reset-state logic.
- Animations must respect `prefers-reduced-motion` (existing media query disables them globally).
- Sound helpers: `tone(freq, start, duration, type, gainPeak)` plus `sfxPickUp/sfxArrive/sfxClick/sfxVictory`. The audio context is created lazily and resumed on the first user gesture (mobile autoplay policy). `#soundToggle` flips `soundOn`.

## Gotchas
- Element references are captured once at script start via `getElementById`; `#wave`, `#dialogue`, and `#restart` start `display:none` and are revealed by JS. Revealed elements also get animation classes (`.enter`, `.show`, `.glow`) — don't skip them when toggling visibility.
- Dragging hit-testing compares the raisin's center against the door rect (with tolerance) inside `checkArrival()` — the game "wins" as soon as the center enters the door zone. `arrive()` sets `done=true`, so the raisin stops being draggable.
- Confetti elements are appended to `#game` with CSS variables `--fallDist`/`--rot` and remove themselves on `animationend`.