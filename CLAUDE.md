# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Classic Tetris in vanilla JavaScript + HTML5 Canvas. No dependencies, no `package.json`, no build step, no tests, no linter. UI text and README are in Spanish.

## Running

Open `index.html` directly, or serve the folder statically (recommended):

```bash
python3 -m http.server 8000   # then open http://localhost:8000
npx serve .
```

## Architecture

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
