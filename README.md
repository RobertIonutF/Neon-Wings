# Neon Wings

Neon Wings is a fast-paced arcade shoot-'em-up built with pure HTML5 Canvas and JavaScript. Pilot a neon starfighter, shred through a cyber-serpent that dives down the screen, stack ridiculous power-ups, and survive all **200 waves** to defeat the **Omega Serpent**.

---

## Contents
- [Overview](#overview)
- [How to Run](#how-to-run)
- [Core Game Loop](#core-game-loop)
- [Controls](#controls)
- [Power-ups](#power-ups)
- [Shop & Upgrades](#shop--upgrades)
- [Difficulty & Options](#difficulty--options)
- [Saving & Continuing](#saving--continuing)
- [Game Structure](#game-structure)
- [How to Modify the Game](#how-to-modify-the-game)
  - [Visual Style & UI](#visual-style--ui)
  - [Waves, Boss & Difficulty](#waves-boss--difficulty)
  - [Player & Power-up Balance](#player--power-up-balance)
  - [Shop Tuning](#shop-tuning)
  - [Input & Controls](#input--controls)
- [Tips for Development](#tips-for-development)

---

## Overview

- **Genre:** Single-screen arcade shooter / boss rush
- **Engine:** Raw HTML5 Canvas + vanilla JavaScript in a single file: `neon-wings.html`
- **Objective:** Destroy the **Serpent Boss** each wave by focusing down the **head segment** while managing its tail, pushback, and descent.
- **Win condition:** Clear all **200 waves** and defeat the **Omega Serpent** on the final wave.
- **Progression:**
  - Enemies gain HP, speed, and aggression as waves increase.
  - The player collects power-ups and uses the shop to scale damage, spread, and fire rate.

---

## How to Run

### Quick Start (no tooling required)
1. Locate `neon-wings.html` in this folder.
2. Open it directly in a modern desktop browser (Chrome, Edge, Firefox):
   - Double-click the file, or
   - Right-click → **Open with** → your browser.
3. The game boots immediately; no build step or server is required.

### Optional: Run via a local web server
If you prefer to serve it over HTTP (useful for browser devtools or mobile testing):

- **Python 3** (from this directory):
  ```bash
  python -m http.server 8000
  ```
  Then open: `http://localhost:8000/neon-wings.html`

- **Node / npm (serve)**:
  ```bash
  npx serve .
  ```

Any simple static server works; there are no backend dependencies.

---

## Core Game Loop

1. **Start game** from the main menu.
2. A segmented **snake/serpent** weaves across and descends toward the bottom of the screen.
3. You move your ship horizontally to dodge and aim.
4. **Shoot segments** to damage them and earn score.
   - Killing **tail segments** far from the head pushes the snake **upwards** and can temporarily pause its descent.
   - Killing the **head** clears the wave.
5. Between moments of danger, use your score in the **shop** to buy upgrades.
6. Survive and repeat through progressively harder waves until reaching the final boss.

---

## Controls

### Keyboard
- **Move ship:** `Arrow Left` / `Arrow Right` or `A` / `D`
- **Fire rockets:** `Space`
- **Open/close shop:** `S`
- **Pause / Resume:** `Esc`

### Mouse (desktop)
- **Move ship:** Move mouse horizontally over the canvas.
- **Hold left click:** Fire bullets (in **Manual** fire mode).
- **Right-click:** Fire rockets.
- Right-click does not open the context menu on the canvas.

### Touch (mobile/tablet)
- **Touch & drag:** Move the ship horizontally.
- **Hold touch:** Fire bullets while pressed (in **Manual** fire mode).

> Note: Rockets on touch devices are currently triggered by `Space` or right-click on desktop; mobile-specific rocket controls can be added if needed.

---

## Power-ups

Each enemy segment can randomly drop a floating power-up. When your ship touches it, the corresponding stat increases and is reflected in the HUD at the bottom.

All core power-ups **stack to high levels** (up to level 50) for over-the-top builds.

- **⚡ Rapid Fire (RAPID)**
  - Increases fire rate (reduces time between shots).
  - HUD: leftmost icon with a level badge.

- **◇ Spread Shot (SPREAD)**
  - Adds more bullet angles.
  - At higher levels you fire wide “fans” of bullets.

- **💥 Damage Boost (DAMAGE)**
  - Increases damage per bullet.
  - Scales slower than enemy HP, so you still need positioning and strategy.

- **⬡ Multi Shot (MULTI)**
  - Adds parallel bullet streams offset horizontally.
  - Great for covering the snake body.

- **🚀 Rockets (ROCKETS)**
  - Grants rocket ammo.
  - Rockets create AoE explosions that damage multiple segments.
  - Ammo count is shown in the ROCKET power-up HUD tile.

---

## Shop & Upgrades

You can spend your **score** in the **Neon Shop** to buy permanent upgrades for the current run.

- Open/close shop: press **`S`** or click the cart button (🛒) at the bottom-left.
- Opening the shop **pauses the game**; closing it resumes.

### Upgrade items
- **RAPID FIRE** – Improves fire rate.
- **SPREAD SHOT** – More bullet angles.
- **DAMAGE BOOST** – Higher bullet damage.
- **MULTI SHOT** – More parallel bullet streams.
- **ROCKET AMMO** – Buy more rockets (`+5` per purchase up to a cap).

Costs scale **exponentially** with current level and are also affected by **difficulty**. Higher difficulty = more expensive upgrades.

---

## Difficulty & Options

From the **main menu**, use:

- **OPTIONS** → configure:
  - **Fire Mode**
    - `AUTOMATIC` – Ship fires automatically while game is running.
    - `MANUAL` – You control firing with left click / touch.
  - **Difficulty**
    - `EASY`, `NORMAL`, `HARD`, `EXTREME`
    - Difficulty affects enemy stats and shop prices via multipliers.

Settings are stored in `localStorage` so they persist between sessions.

---

## Saving & Continuing

The game uses browser **localStorage** to save progress.

### What is saved
- Current **score**
- Current **wave number**
- Player position
- Power-up levels: Rapid, Spread, Damage, Multi, Rockets
- Timestamp (for reference)

### When saving happens
- **Automatically:** After each wave, when the serpent head dies and the wave is cleared.
- **Manually:**
  - Pause the game with **`Esc`**.
  - On the pause screen, click **SAVE GAME**.
  - A `✓ GAME SAVED` message appears briefly.

> The game intentionally uses **wave checkpoints**. On continue, you restart at the **beginning** of the saved wave rather than resuming mid-wave state.

### Continuing a game
- On startup, if a save exists, the **CONTINUE** button appears on the main menu.
- Click **CONTINUE** to restore stats and wave and start from that checkpoint.

### Clearing the save
- Losing (Game Over) or starting a brand-new game clears the existing save.

---

## Game Structure

This project is intentionally minimal:

- **Single file:** `neon-wings.html`
- Inside that file you’ll find:
  - `<style>` block: full CSS for layout, HUD, screens, and neon theme.
  - `<canvas id="c">`: the main gameplay surface.
  - HUD overlays and screens (`startScreen`, `optionsScreen`, `helpScreen`, `pauseScreen`, `shopScreen`, `overScreen`, `victoryScreen`).
  - A large `<script>` block with:
    - Object pools for bullets, rockets, explosions, particles, floating text.
    - Player state and power-up logic.
    - Snake (serpent) generation, movement, HP scaling, and wave logic.
    - Shop system and upgrade pricing.
    - Save/load system using `localStorage`.
    - Input handling for keyboard, mouse, and touch.
    - Main `update()` and `draw()` loops.

All major systems are separated by big comment headers like:

- `// === GAME CONSTANTS ===`
- `// === SNAKE INIT ===`
- `// === SHOOTING ... ===`
- `// === SHOP SYSTEM ===`
- `// === SAVE/LOAD SYSTEM ===`
- `// === CONTROLS ===`

Use these as anchors when editing.

---

## How to Modify the Game

You can open `neon-wings.html` in any code editor and tweak behavior directly.

### Visual Style & UI

Look at the top of the file in the `<style>` block.

- **Theme colors:**
  - Defined in the `:root` CSS variables:
    - `--cyan`, `--pink`, `--yellow`, `--green`, `--orange`, `--red`, `--bg`, `--surface`, `--border`.
  - Change these to retheme the entire game.

- **HUD & screens:**
  - Classes like `.top-bar`, `.powers`, `.screen`, `.shop-screen`, `.help-screen`, etc.
  - You can adjust fonts, sizes, spacing, and positioning.

- **Animations:**
  - Keyframes such as `waveAnim`, `victoryPulse`, `superBossPulse` control various effects.

### Waves, Boss & Difficulty

Search within the `<script>` for these constants and functions:

- **Wave limits**
  - `const MAX_WAVE = 200;`
  - `const SUPER_BOSS_WAVE = 200;`
  - Change these to shorten/extend the campaign or move the final boss.

- **Snake & HP scaling**
  - `getSegmentHP(wave, isHead)` – controls HP of tail segments and boss head (including super boss multipliers).
  - `getSnakeSpeed(wave)` – controls snake movement speed per wave.
  - `getDescendInterval(wave)` – how frequently the snake drops down.
  - `getDescendAmount(wave)` – how far it drops per descent.
  - `getSegmentCount(wave)` – number of segments per wave.

Tuning these will drastically change the difficulty curve.

### Player & Power-up Balance

Key variables and functions to adjust:

- **Caps**
  - `const MAX_POWER_LEVEL = 50;`
  - `const MAX_ROCKETS = 99;`

- **Fire rate & damage**
  - `getFireRate()` – maps `P.rapid` level to milliseconds between shots.
  - `getDamage()` – maps `P.damage` level to bullet damage.
  - `getSpreadCount()` – bullets per shot from spread.
  - `getMultiCount()` – parallel streams.

- **Rockets**
  - `fireRocket()` – uses rocket ammo and spawns a rocket.
  - `createExplosion()` – explosion radius, visual effects, and AoE damage.

- **Pushback & strategy**
  - `handleSegmentDeath(s, index)`:
    - Tail kills apply **pushback** to the snake and can **pause descent**.
    - `totalPushback` and `snake.descPaused` durations determine how rewarding it is to target far tail segments.

Adjusting these lets you make builds more/less overpowered, or make the game more bullet-hell-like.

### Shop Tuning

Search for `// === SHOP SYSTEM ===`.

- **Base costs**
  - `SHOP_BASE_COSTS` object controls base prices.
- **Scaling**
  - `getUpgradeCost(type, currentLevel)` uses exponential scaling and difficulty multipliers.
- **Difficulty multipliers**
  - `DIFFICULTY_MULTIPLIERS` under the **settings** section map `easy`, `normal`, `hard`, `extreme` to numeric multipliers.

You can:
- Make upgrades cheaper/more expensive.
- Make particular stats (e.g., damage) scale faster or slower.
- Give higher difficulties steeper shop pricing.

### Input & Controls

See the **CONTROLS** section near the end of the script:

- Keyboard events:
  - `keydown` / `keyup` handlers manage movement, pause, shop, and rockets.
- Mouse & touch:
  - Events on the `canvas` (`mousemove`, `mousedown`, `mouseup`, `touchstart`, `touchmove`, `touchend`) control ship movement and firing.

You can:
- Remap keys.
- Add alternate controls for rockets on touch.
- Implement a virtual joystick or on-screen buttons for mobile.

---

## Tips for Development

- Use browser **DevTools** (F12) while running the game to:
  - Inspect console logs and catch errors.
  - Tweak CSS live.
  - Set breakpoints in the script or edit constants on the fly.
- Keep a backup of `neon-wings.html` before big changes.
- Because everything is in one file, it’s easy to:
  - Duplicate the file as a variant (e.g., `neon-wings-hardcore.html`).
  - Experiment with different balance curves without breaking the original.

Have fun tweaking Neon Wings, and feel free to turn it into your own custom boss-rush shooter!