# AGENTS.md

## Overview
A single-file vanilla HTML/CSS/JS game ("Pasa Pasa"): drag the raisin (🍇 pasa) to the house door to trigger a short dialogue with the wave (🌊 ola); the raisin then walks into the house and a completion counter goes up. All UI text and dialogue is in Spanish — keep new strings in Spanish.

## No build system
- `index.html` is the entire app: inline `<style>`, markup, and one inline `<script>`. No dependencies, no bundler, no npm, no server needed.
- To test: open `index.html` directly in a browser. There is no test suite or lint/typecheck step.
- Do not introduce a build tool, framework, or external assets unless asked — the file is intentionally self-contained. Graphics are Canvas 2D paths (no images); sounds use the Web Audio API (no audio files).

## Architecture (hybrid: canvas world + DOM UI)
- The world (sky, sea, sand, palm, house, raisin, wave, confetti) is drawn on `<canvas id="stage">`, which fills `#game` (width `min(100vw,480px)`, height `100vh`, max 900px). UI stays as HTML on top: `#soundToggle`, `#counter`, `#githubLink`, `#hint`, `#dialogue` (`aria-live`), `#end` with `#restart`. Keep text and buttons in the DOM (accessibility, crisp text), not in the canvas.
- One `requestAnimationFrame` loop: `frame()` → `update(dt)` → `render()`. `dt` is in seconds, clamped to 1/30; use it for all motion.
- `layout()` (on load and `resize`) sizes the canvas for `devicePixelRatio` and computes positions into `L` (`hz` = horizon at 57% height, `house`, `door`, `start`, `wave`, `palm`). The `draw*` functions use local coordinates taken from the original CSS boxes.
- Game objects are plain objects filled by `reset()`: `R` (raisin; `x,y` are its feet in px, `s` its perspective scale from `scaleAt(y)`), `Wv` (wave), `Door` (`open`, `glow`, `light`, each 0..1).
- Motion helpers: `spring(s, target, k, d, dt)` and `kick(s, impulse)` for lift, squash & stretch, tilt and the wave pop; `tween(dur, fn, ease)` returns a promise advanced by the loop (`wait`, `walkTo` build on it).

## Code conventions (differ from defaults)
- Short variable names, `'use strict'` inside a single IIFE `(() => { ... })()`. Keep everything scoped to that IIFE; no globals.
- CSS uses `:root` custom properties (`--sky-top`, `--sand`, `--purple`, …) and section comments (`/* ---------- Dialogue ---------- */`); JS uses `// ---------- X ----------`. The canvas reads the palette once into `C` via `getComputedStyle`, so shared colors stay in `:root`.
- Input is unified pointer events: `pointerdown` on the canvas hit-tests the raisin (`hitRaisin()`) and uses `setPointerCapture`; `pointermove`/`pointerup`/`pointercancel` on `window` (`pointermove` is `passive:false`). The pointer position is applied in `update()` via `followPointer()`, which also measures the throw velocity used for inertia after release. Keyboard: arrows on the focused canvas (hold to walk at `KEY_SPEED`, a tap nudges ~14px).
- Game state lives in vars at the top of the IIFE (`dragging`, `done`, `step`, `offsetX/Y`, `soundOn`, `audioCtx`, `count`, `gen`, …). Dialogue: `arrive()` shows step 0, the `#next` handler goes 0 → 1 → 2, then `step = 3` runs `finale()` (door opens, raisin walks in, counter +1, `#end` shows).
- `#restart` calls `reset(true)`: in-game reset, no reload. Any new state must be reset there.
- Completion counter: `count` in `localStorage` key `pasaPasa.completadas` (`loadCount`/`saveCount`, wrapped in try/catch). Shown in `#counter` and counted up in `#end`.
- Respect `prefers-reduced-motion`: the CSS media query disables DOM animations, and the JS flag `reduce` (live via `matchMedia`) freezes the ambient clock `T`, snaps springs, makes tweens instant, and skips confetti and throw inertia. New motion must go through these.
- Sound helpers: `tone(freq, start, duration, type, gainPeak)`, `sweep(f0, f1, start, duration, type, gainPeak)` plus `sfx*` helpers. The audio context is created lazily and resumed on the first user gesture (`unlockAudio()`, mobile autoplay policy). `#soundToggle` flips `soundOn`.

## Gotchas
- Async finale steps re-check `g !== gen` after every `await`: `reset()` bumps `gen` and drops pending tweens, so stale steps must bail out. Follow that pattern for new async sequences.
- `checkArrival()` runs every frame (dragging, sliding with inertia, or keyboard) and compares the raisin's body centre with the door rect (±18px horizontally, ±40px vertically). `arrive()` sets `done=true`; after that the raisin only moves by tweens (`R.auto`).
- Ambient animation uses `T` (frozen with reduced motion); timers that must always run use `clock`.
- `render()` draw order is the z-order; raisin and wave are depth-sorted by `y`.
- `#dialogue` and `#end` start `display:none`; revealed elements get their animation class through `replay(el, cls)` (`.show`, `.bump`).
- Hidden tabs throttle `requestAnimationFrame` and `dt` is clamped, so the game (tweens included) effectively pauses while the tab or browser pane is hidden. Keep that in mind when testing.
