# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

A classic Tetris implementation in vanilla JavaScript with HTML5 Canvas. No build process, no package manager, no external dependencies — just three files: `index.html`, `style.css`, `game.js`.

## Running the game

There is no build/test/lint tooling. To run it, open `index.html` directly or serve the directory statically:

```bash
python3 -m http.server 8000   # or: npx serve .
```

Then verify changes by opening the page in a browser and playing (arrow keys to move, `↑`/`X` to rotate, `↓` soft drop, `Space` hard drop, `P` to pause).

## Architecture

Everything lives in `game.js` as module-level state and functions (no classes, no build-time modules — it's a single script loaded via `<script src="game.js">`).

- **Board model**: `board` is a `ROWS × COLS` (20×10) matrix; each cell is `0` (empty) or a color index `1–7` identifying which piece type placed it.
- **Pieces**: `PIECES` defines the 7 tetrominoes as square matrices (index 0 is unused/`null` so piece type indices line up with `COLORS`). `current` and `next` are piece objects `{ type, shape, x, y }`.
- **Rotation**: `rotateCW` transposes + reverses rows of the shape matrix. `tryRotate` applies the rotation and attempts wall kicks (`[0, -1, 1, -2, 2]` column offsets) until one doesn't collide, otherwise the rotation is discarded.
- **Collision**: `collide(shape, ox, oy)` is the single source of truth for whether a shape can occupy a position — checks board bounds and existing locked cells. Used by movement, rotation, ghost-piece projection, and spawn (game-over check).
- **Game loop**: `loop(ts)` runs via `requestAnimationFrame`, accumulating elapsed time in `dropAccum` against `dropInterval`; when exceeded, the piece drops one row or locks if blocked.
- **Locking a piece**: `lockPiece()` → `merge()` (bakes the piece into `board`) → `clearLines()` → `spawn()` (promotes `next` to `current`, generates a new `next`, and checks for game over via `collide` at spawn position).
- **Line clearing**: `clearLines()` scans bottom-to-top, splicing out full rows and unshifting empty ones at the top; score/level/`dropInterval` are recalculated when lines are cleared.
- **Scoring**: `LINE_SCORES = [0, 100, 300, 500, 800]` multiplied by `level` for line clears; hard drop adds 2 points/row, soft drop adds 1 point/row.
- **Leveling/speed**: level increases every 10 lines; `dropInterval = max(100, 1000 - (level-1) * 90)` ms.
- **Ghost piece**: `ghostY()` projects `current` straight down until it would collide, and `draw()` renders it at low alpha before the real piece.
- **Rendering**: `draw()` redraws the whole board canvas each frame (grid, locked cells, ghost, current piece); `drawNext()` renders the next-piece preview on a separate small canvas.
- **Input**: a single `keydown` listener dispatches on `e.code` for movement/rotation/drops/pause; `P` works even while paused/game-over, other keys are ignored in those states.

## Tunable constants (in `game.js`)

`COLS`, `ROWS`, `BLOCK` (cell pixel size), `COLORS`, `LINE_SCORES`, initial `dropInterval`. If `COLS`/`ROWS`/`BLOCK` change, update the `<canvas id="board">` `width`/`height` in `index.html` to match (`COLS × BLOCK` and `ROWS × BLOCK`).
