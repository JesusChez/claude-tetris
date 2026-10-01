# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

<<<<<<< HEAD
## Project

Classic Tetris in vanilla JavaScript + HTML5 Canvas. No dependencies, no `package.json`, no build step, no tests, no linter. UI text and README are in Spanish.

## Running

Open `index.html` directly, or serve the folder statically (recommended):

```bash
python3 -m http.server 8000   # then open http://localhost:8000
npx serve .
=======
## Running the game

No build step or dependencies. Open directly or serve with any static server:

```bash
open index.html                  # macOS direct open
python3 -m http.server 8000      # then visit http://localhost:8000
>>>>>>> 5b4128123529aea4154336cb779f2a8412050111
```

## Architecture

<<<<<<< HEAD
Three files: `index.html` (DOM + two canvases + overlay), `style.css` (dark theme), and `game.js` (all logic, plain script, no modules).

Key points in `game.js`:

- **State** is a set of module-level `let` globals (`board`, `current`, `next`, `score`, `lines`, `level`, `paused`, `gameOver`, `dropInterval`, `animId`…). `init()` resets all of them and is also the restart handler.
- **Board** is a `ROWS × COLS` matrix; each cell is `0` or a piece type index `1–7`. That same index is used in piece shapes and as the index into `COLORS` and `PIECES` (index 0 is `null`), so these three arrays must stay aligned.
- **Pieces** are square matrices; rotation is `rotateCW` (transpose + reverse) with simple horizontal wall kicks `[0, -1, 1, -2, 2]` in `tryRotate`.
- `collide(shape, x, y)` is the single source of truth for movement, rotation, ghost projection (`ghostY`), and game-over detection (a freshly spawned piece colliding in `spawn()` calls `endGame()`).
- **Piece lifecycle**: `lockPiece()` → `merge()` → `clearLines()` (scoring/level/speed) → `spawn()` (promotes `next` to `current`, redraws the next-piece preview).
- **Game loop**: `requestAnimationFrame`-based `loop(ts)` accumulates elapsed time and drops the piece once `dropAccum >= dropInterval`, then redraws everything. Input is handled by a single `keydown` listener using `e.code`.
- **Scoring**: `LINE_SCORES[cleared] * level`; soft drop +1/row, hard drop +2/row. Level = `floor(lines / 10) + 1`; `dropInterval = max(100, 1000 - (level - 1) * 90)`.

## Gotchas

- Changing `COLS`, `ROWS`, or `BLOCK` requires updating the `<canvas id="board">` `width`/`height` in `index.html` (`COLS*BLOCK × ROWS*BLOCK`). The next-piece canvas (120×120) assumes a 4×4 grid of 30px cells (`NB` in `drawNext`).
- `loop()` always reschedules itself after running, so a `cancelAnimationFrame(animId)` issued from inside it (e.g. `endGame()` triggered via `lockPiece` → `spawn`) does not stop the loop.
- `togglePause()` shows the overlay on pause but does not hide it on resume.
=======
Three files, no framework, no bundler:

- **`index.html`** — DOM structure: `<canvas id="board">` (300×600px) for the playfield, `<canvas id="next-canvas">` (120×120px) for the preview, sidebar HUD (`#score`, `#lines`, `#level`), and a shared overlay `#overlay` for both PAUSE and GAME OVER states.
- **`style.css`** — Dark/retro arcade theme; uses CSS variables, flexbox, and `backdrop-filter` on overlays.
- **`game.js`** — All game logic (~305 lines, `'use strict'`, no modules).

### game.js internals

| Concern | Key identifiers |
|---|---|
| Board state | `board` — `ROWS×COLS` matrix; `0` = empty, `1–7` = piece color index |
| Piece representation | `{ type, shape, x, y }` where `shape` is a 2-D matrix |
| Rotation | `rotateCW(shape)` — transpose + reverse; `tryRotate()` applies wall kicks `[0,±1,±2]` |
| Collision | `collide(shape, ox, oy)` — checks bounds and board occupancy |
| Game loop | `loop(ts)` via `requestAnimationFrame`; `dropAccum` tracks elapsed ms against `dropInterval` |
| Line clear | `clearLines()` — iterates board bottom-up, splices full rows, prepends empty row |
| Scoring | `LINE_SCORES = [0,100,300,500,800]` × `level`; hard drop +2/cell, soft drop +1/row |
| Speed | `dropInterval = max(100, 1000 − (level−1) × 90)` ms; level = `floor(lines/10) + 1` |
| Ghost piece | `ghostY()` — projects current piece down until collision; drawn at `globalAlpha = 0.2` |
| State flags | `paused`, `gameOver`, `animId` (RAF handle) |

### Game flow

`init()` → `spawn()` → `requestAnimationFrame(loop)`. Each frame: accumulate dt → auto-drop or `lockPiece()` → `draw()`. `lockPiece()` = `merge()` + `clearLines()` + `spawn()`. If `spawn()` immediately collides → `endGame()`.

## Tunable constants (top of game.js)

`COLS` (10), `ROWS` (20), `BLOCK` (30 px), `COLORS` (array indexed 1–7), `LINE_SCORES`. If you change `COLS`/`ROWS`/`BLOCK`, update the canvas `width`/`height` attributes in `index.html` to match (`COLS×BLOCK` and `ROWS×BLOCK`).
>>>>>>> 5b4128123529aea4154336cb779f2a8412050111
