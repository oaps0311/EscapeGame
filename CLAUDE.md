# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Single-file browser-based escape game ("Hospital Escape - The Forgotten Thicket") written entirely in one `index.html` — HTML, CSS, and vanilla JavaScript with no build tools, dependencies, or frameworks. The game is in French, created as a 21st birthday gift.

## Running

Open `index.html` directly in a browser. No server or build step needed. The game references local image files (room21.png, corridor.png, aldwyn.png, patient13.png, pharmacy.png, office.png, basement.png) that should be in the same directory.

## Architecture

Everything lives in `index.html`:
- **CSS** (lines 8–185): Inline `<style>` block with scene themes (black/white/grey/dark/forest), animations (glitch, shake, fade-in, typewriter cursor), card/door/panel components, and responsive layout.
- **JavaScript** (lines 187–982): Inline `<script>` block with a state-machine game engine.

### Game Engine Pattern

- **`gameState`** object holds all mutable state: current room, phase (sub-step within a room), inventory, clue/search flags, puzzle inputs, and alert flags.
- **`ROOMS`** enum maps room names to string keys.
- **`renderRoom()`** is the central dispatcher — switches on `gameState.room` and calls the appropriate `render*()` function, then updates inventory display.
- Each room renderer (e.g., `renderRoom21()`, `renderAldwyn()`, `renderCorridor1()`) builds HTML via template literals or DOM creation and sets `gameEl.innerHTML`.
- Multi-phase scenes (wake-up, Aldwyn, exit) use `gameState.phase` to sequence typewriter text across multiple render passes.
- **`goToRoom(room, delay)`** handles transitions: triggers a blackout effect, updates state, and re-renders.

### Game Flow

WAKE_UP (5 phases) → ROOM (Room 21, code puzzle: 7302) → ALDWYN (5 phases, story) → CORRIDOR_1 (hub: Patient Room 13, Pharmacy, West Wing) → CORRIDOR_2 (Office, Basement) → BASEMENT (light switches puzzle) → EXIT (5 phases) → FREEDOM (final message).

Key items: note (from Patient 13), badge (pharmacy trash, requires note), basement_key (office, requires password "aldwyn" then year 2003/2004/2005).

### Puzzles

1. **Room 21 digicode**: 4-digit code (7302), clues hidden in interactive cards
2. **Patient 13 dialogue**: binary choice affects whether you get the note
3. **Pharmacy search**: find badge in trash (only available if you have the note)
4. **Office computer**: password "aldwyn", then year-of-birth code unlocks basement key
5. **Basement switches**: 5 toggles — first+last ON, third OFF, remaining two are opposites

### Key Functions

- `typeWriter()` — animated text reveal with cursor
- `flash()` — temporary notification messages
- `doEffect()` — CSS class-based visual effects (glitch, shake, black)
- `foundClue()` / `searchPharma()` — mark items as discovered and re-render
- `updateInventory()` — syncs inventory panel with `gameState.inventory`

## Conventions

- All UI text is in French.
- CSS uses short class names (`.mid`, `.hdr`, `.sw`, `.wt`) for compactness.
- No external JS dependencies; DOM manipulation is direct (innerHTML + template literals).
- Visual effects are CSS animation classes toggled via JS timeouts.
