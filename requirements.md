# Project Requirements & Roadmap: Bemdoku

## Overview
Bemdoku is a modular Sudoku web application developed in parallel across two primary tracks:
- **Track A (Engine)**: Pure TypeScript Sudoku engine (board math, backtracking solver, procedural generator, human-technique rating ladder).
- **Track B (App / UI)**: React front-end (state machine, input management, accessibility, animations, responsive design).

---

## Architecture & Directory Structure

```text
src/
  types/          shared types: Board, Cell, Difficulty, Puzzle, GameState
  engine/         pure TypeScript, NO React imports
     board/        indexing helpers, peers (row/col/box), validity checks
     solver/       backtracking solver with solution limit
     generator/    full-grid fill, cell digging, seeded random
     rating/       human-style solver, technique ladder, difficulty rating
     index.ts      the ONLY file the UI imports from
  state/          game state model: reducer, undo/redo history, notes
  hooks/          React hooks that wrap state + engine (useGame, useTimer)
  components/     Board, Cell, NumberPad, Controls, DifficultyPicker, WinDialog
  workers/        Web Worker for generation (later)
  data/           pre-generated puzzle bank (JSON), optional
  tests/          mirrors engine/ and state/
```

---

## Handoff Rules & Engineering Standards

1. **Strict Engine Isolation**: The UI never reaches into engine internals; all engine interactions must pass strictly through `src/engine/index.ts`.
2. **Pure Functions**: Engine functions must be pure and deterministic — identical inputs (plus seed) always produce identical outputs.
3. **No React in Engine**: The `engine/` directory must contain zero React dependencies or browser DOM bindings.
4. **Test-First Delivery**: Every engine step must ship with passing unit tests before the UI integrates or depends on it.
5. **Cross-Review**: Review each other's pull requests so both developers retain full context across the entire application lifecycle.

---

## Development Phases

### Phase 1: Core (Parallel Track)

#### Person A: Engine
- [ ] **Board helpers & validity checks**:
  - Indexing utilities (row, column, 3x3 block conversions).
  - Peer lookups (row peers, column peers, box peers).
  - Board validity checkers (duplicates across row/col/box).
  - Unit tests mirroring in `src/tests/engine/board/`.
- [ ] **Backtracking solver**:
  - Solve standard grids and return single solution.
- [ ] **Solution counting & uniqueness constraint**:
  - Extend backtracking solver to count solutions up to a limit (early return / stop at 2 to prove uniqueness).
- [ ] **Randomized full-grid generator**:
  - Seeded PRNG support.
  - Complete, valid 9x9 grid generator.
- [ ] **Dig-holes loop with uniqueness check**:
  - Iteratively remove clues while verifying uniqueness after every removal.
- [ ] **Engine stress testing**:
  - Generation benchmark over thousands of puzzles to verify validity, uniqueness guarantee, and execution latency.

#### Person B: App (UI & State)
- [ ] **Static board UI**:
  - 9x9 grid layout with distinct 3x3 box borders.
  - Distinct styling for givens vs. player-entered cells.
- [ ] **Navigation & inputs**:
  - Cell selection state.
  - Full keyboard navigation (Arrow keys, numbers 1-9, Backspace / Delete).
- [ ] **Touch/Mouse controls**:
  - On-screen NumberPad and action buttons.
- [ ] **Game state model**:
  - Reducer covering `board`, `selectedCell`, `notes`, `mistakes`, and `history` (undo/redo).
- [ ] **Mock integration**:
  - Hardcoded sample puzzle integration for standalone testing until engine APIs land.

---

### Phase 2: Integration

- [ ] **API Exposure & Consumption**:
  - Person A publishes public engine surface in `src/engine/index.ts`.
  - Person B replaces hardcoded puzzle mock with `generate(difficulty)`.
- [ ] **New Game & Difficulty Flow**:
  - Person B implements DifficultyPicker modal/controls and game reset lifecycle.
- [ ] **Difficulty Tiers (Clue-Count Based)**:
  - Person A defines clue count ranges per difficulty tier.
- [ ] **Interactive Visual Feedback (Both)**:
  - Conflict / duplicate error highlighting.
  - Same-number highlighting across the board.
  - Win condition detection and celebration dialog.
- [ ] **Pencil Notes & Undo/Redo (Both)**:
  - Toggleable pencil note mode.
  - Action history stack for reversible moves and notes updates.

---

### Phase 3: Difficulty & Polish

#### Person A: Engine & Rating
- [ ] **Human-style solver**:
  - Candidate grid tracking.
  - Naked singles & hidden singles resolution logic.
- [ ] **Advanced techniques & difficulty ladder**:
  - Pointing pairs/triples, naked/hidden pairs.
  - Technique log analysis to compute difficulty score.
- [ ] **Generation tuning loop**:
  - Rejection / retry loop to guarantee generated puzzle strictly aligns with target difficulty rating.
- [ ] **Puzzle bank**:
  - Pre-generate and serialize puzzle bank in `src/data/puzzles.json` for computationally heavy tiers.

#### Person B: UI Polish & Worker
- [ ] **Hint feature**:
  - Next-step deduction explanation powered by engine technique log.
- [ ] **Persistence & Timer**:
  - Stopwatch timer with pause/resume support.
  - `localStorage` game state caching (auto-save and restore).
- [ ] **Web Worker background generation**:
  - Offload heavy generation loops to `src/workers/generator.worker.ts` with responsive UI loading indicator.
- [ ] **Responsive Design & Accessibility**:
  - Mobile, tablet, and desktop adaptive layouts.
  - ARIA attributes, keyboard focus management, and WCAG-compliant contrast ratios.

---

### Phase 4: Wrap-up & Release

- [ ] **Playtesting & Calibration**:
  - Playtest each tier with real users; calibrate difficulty thresholds and technique ladders.
- [ ] **Automated Testing Suite**:
  - Unit tests for all engine and state logic.
  - React component tests and smoke tests.
- [ ] **Production Deployment**:
  - Static build configuration and deployment pipeline.
  - Documentation (`README.md`), user guide, and documented known limitations.
