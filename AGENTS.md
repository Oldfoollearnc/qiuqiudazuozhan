# 球球大作战 — Agent Guide

## Structure

- **Single file** `index.html` (~1175 lines). All HTML, CSS, JS in one file.
- No build tools, no dependencies, no package.json.
- Open with `start index.html` or double-click. For audio to work, must first click the page (browser AudioContext policy).

## GitHub Pages Deployment

- Push `master` → auto-deploys to https://oldfoollearnc.github.io/qiuqiudazuozhan/
- No separate `gh-pages` branch. Source lives at root of `master`.

## Code Architecture

### Global State
- `gameRunning` — shared between demo and real game
- `player` — current human player object
- `players[]` — all players (bots + human)
- `particles[]` — global particle array, drawn in draw loop
- `frameCount` — global frame counter, used for animation timing in `drawCell`

### Lifecycle
1. Page load → `startDemo()` creates 17 bots → sets `gameRunning=true` → `demoLoop()` (own RAF)
2. Click start → `startGame` wrapper calls `stopDemo()` then original `startGame`
3. `startGame` → creates player + bots → `gameLoop()` (own RAF via `update()` + `draw()`)
4. ESC / death → `quitGame()` → calls `startDemo()` + `startBGM()` again

### Key Patterns
- Chat (`chatOpen=true`) freezes player movement in `update()`
- Custom cursor: `canvas{cursor:none}` in CSS, teal pulsing ring drawn at `(mouse.x, mouse.y)` after each frame
- Skins: `p.skin` on player object, `p.skinState` for per-player animation state. 9 skins (`ALL_SKINS`). `drawCell(c, p)` switches on `p.skin`.
- AI: 3 personalities (0=aggressive, 1=balanced, 2=defensive). Spike-aware pathing, prey interception, auto-split when dominant.
- Config: all tunables in `CFG` object (MAP, speeds, durations, etc.)
- Collision: mass-based eating (`totalMass()`). Player consumed if `other.mass > c.mass * 1.15`.
- Sound: Web Audio synthesis via `AudioContext`. `initAudio()` creates context on first user interaction. 8 SFX + BGM.

### Style
- Chinese UI labels throughout
- `#ui` has `pointer-events:none`, interactive children opt in with `pointer-events:auto`
- All measurements in canvas coordinates. Camera transform via `ctx.translate` + `ctx.scale`.
- **Git 提交信息必须使用中文。**
