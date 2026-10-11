# Bemdoku

A modern, high-performance Sudoku web application built with a decoupled architecture: a pure TypeScript engine and an accessible React user interface.

---

## 🧩 Architectural Overview

Bemdoku separates logic and presentation into two independent tracks:

- **Engine (`src/engine/`)**: Pure TypeScript mathematical core. No React imports, no DOM access, completely deterministic and pure. It handles board indexing, fast backtracking solving, procedural puzzle generation with seeded random number generation, and human technique difficulty rating.
- **UI & State (`src/components/`, `src/state/`, `src/hooks/`)**: Modern React interface featuring responsive layout, keyboard & touch input handling, notes (pencil marks), undo/redo history, timer, and accessibility.

The UI interacts with the engine exclusively through [`src/engine/index.ts`](file:///Users/iolochristopher/Documents/bemdoku/src/engine/index.ts).

---

## 📁 Project Structure

```text
src/
├── types/          # Shared TypeScript type definitions (Board, Cell, Difficulty, Puzzle, GameState)
├── engine/         # Pure TypeScript engine (NO React or DOM imports)
│   ├── board/      # Indexing helpers, peers (row/col/box), validity checks
│   ├── solver/     # Backtracking solver with solution limit / uniqueness check
│   ├── generator/  # Full-grid generation, hole digging, seeded RNG
│   ├── rating/     # Human-style candidate solver, technique ladder, difficulty rating
│   └── index.ts    # The ONLY public entry point for UI consumers
├── state/          # Game state model: reducer, undo/redo history, notes
├── hooks/          # React hooks wrapping state + engine (useGame, useTimer)
├── components/     # UI components (Board, Cell, NumberPad, Controls, DifficultyPicker, WinDialog)
├── workers/        # Web Worker for background puzzle generation
├── data/           # Pre-generated puzzle banks (JSON)
└── tests/          # Unit and integration tests mirroring engine/ and state/
```

---

## 🚀 Key Features

- **Decoupled Engine Core**: All engine functions are pure; same inputs and seed guarantee identical output.
- **Procedural Puzzle Generator**: Generates valid, unique-solution puzzles with custom hole-digging algorithms.
- **Human-Style Rating Ladder**: Rates puzzles using deductive logic (singles, pointing pairs, candidate elimination) rather than arbitrary clue counts alone.
- **Rich User Experience**:
  - Full keyboard navigation (Arrow keys, 1-9, Delete/Backspace).
  - Mouse and touch-friendly on-screen NumberPad.
  - Conflict highlighting and duplicate number detection.
  - Same-number highlighting across the 9x9 grid.
  - Pencil notes mode with automatic and manual updates.
  - Full undo/redo move history.
  - Timer with pause and auto-save / resume via `localStorage`.
- **Web Worker Offloading**: Computationally intensive puzzle generation runs off the main thread.

---

## 📋 Handoff Rules

1. **Strict UI Isolation**: UI components and state must never import from `src/engine/*` subfolders directly. Only import through [`src/engine/index.ts`](file:///Users/iolochristopher/Documents/bemdoku/src/engine/index.ts).
2. **Pure Functions**: Engine functions must have no side effects and must be deterministic given the same inputs.
3. **Zero React in Engine**: The `src/engine/` directory must contain zero React dependencies or browser DOM bindings.
4. **Test-First Engine Deliveries**: Every engine capability must ship with unit tests in `src/tests/engine/` before the UI consumes it.

---

## 🗺️ Roadmap & Detailed Requirements

For the detailed 4-phase delivery plan and person-by-person task tracking, check [`requirements.md`](file:///Users/iolochristopher/Documents/bemdoku/requirements.md).
