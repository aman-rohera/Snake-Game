# Code Smell Audit — Lab-1 Head (`feat/multiplayer`)

**Repository:** Snake-Game (`feat/multiplayer` commit `d2c36c7`)  
**Target:** `snake_game.cpp` (1132 lines)  
**Evaluator:** Google Antigravity (using `detect-code-smells` and `review-accuracy-calibration`)

---

## 1. Summary of Identified Smells

| Smell Name | Category | File & Location | Confidence | Severity | Status vs Main |
| --- | --- | --- | --- | --- | --- |
| **Duplicate Code / Parallel State** | Dispensables | `snake_game.cpp:121-141, 940-1065` | **C4** (Certain) | HIGH | **INTRODUCED** |
| **Long Method** | Bloaters | `snake_game.cpp:869-1073` | **C4** (Certain) | HIGH | **EXPANDED** |
| **Global Mutable State** | Couplers | `snake_game.cpp:144-145` | **C3** (High) | HIGH | **EXPANDED** |
| **Dead Code** | Dispensables | `snake_game.cpp:63` | **C4** (Certain) | HIGH | UNTOUCHED |
| **Dead Code** | Dispensables | `snake_game.cpp:140` | **C3** (High) | MEDIUM | UNTOUCHED |
| **Large Class / Monolithic State** | Bloaters | `snake_game.cpp:120-142` | **C3** (High) | HIGH | UNTOUCHED |
| **Long Method** | Bloaters | `snake_game.cpp:304-366` | **C3** (High) | MEDIUM | UNTOUCHED |
| **Switch Statements** | OO Abusers | `snake_game.cpp:308-366` | **C3** (High) | MEDIUM | UNTOUCHED |
| **Duplicate Code** | Dispensables | `snake_game.cpp:408-460` | **C3** (High) | MEDIUM | UNTOUCHED |
| **Primitive Obsession** | Bloaters | `snake_game.cpp:61,230` | **C2** (Medium) | LOW | UNTOUCHED |

---

## 2. Detailed Smell Breakdown

### 1. Duplicate Code / Parallel State (INTRODUCED)
- **Site:** `snake_game.cpp:121-141` (GameState) and `snake_game.cpp:940-1065` (runGame)
- **Confidence:** **C4 (Certain)** — Direct duplicate implementation of Player 2 state and step logic alongside Player 1.
- **Description:** Rather than introducing a `Player` class or array abstraction, the Lab-1 PR added parallel fields (`snake2`, `dir2`, `score2`, `goldenApplesEaten2`, `poisonApplesEaten2`, `segmentsToGrow2`, `segmentsToShrink2`) and duplicated all movement, eating, growing, shrinking, tail popping, and rendering loops verbatim for Snake 2.

### 2. Long Method — `runGame()` (EXPANDED)
- **Site:** `snake_game.cpp:869-1073` (205 lines)
- **Confidence:** **C4 (Certain)** — Method expanded by 30 lines with duplicated player collision & eating logic.
- **Description:** `runGame()` accumulated dual-snake collision checking (head-to-head, body-to-body, wall-to-head), dual food consumption logic, and separate power-up checks for P1 vs P2 in the main game loop.

### 3. Global Mutable State — Inter-Thread Statics (EXPANDED)
- **Site:** `snake_game.cpp:144-145`
- **Confidence:** **C3 (High)** — Added `static atomic<Dir> inputDir2` alongside `inputDir`.
- **Description:** Added a second global atomic variable to pass input from `inputThreadFunc()` to `runGame()`, reinforcing global state coupling.

### 4. Dead Code — `MAGNET` Power-Up (UNTOUCHED)
- **Site:** `snake_game.cpp:63`
- **Confidence:** **C4 (Certain)** — Zero gameplay effect logic checking `MAGNET`.
- **Description:** `MAGNET` enum value remains unused in gameplay logic.

### 5. Dead Code — Unpopulated `moveHistory` (UNTOUCHED)
- **Site:** `snake_game.cpp:140`
- **Confidence:** **C3 (High)** — `moveHistory` remains unpopulated in `runGame()`.
- **Description:** Replay feature remains non-functional due to empty history vector.

### 6. Large Class / Monolithic Data Structure — `GameState` (UNTOUCHED)
- **Site:** `snake_game.cpp:120-142`
- **Confidence:** **C3 (High)** — Monolithic state expanded without modular separation.
- **Description:** `GameState` grew larger by absorbing all Player 2 attributes directly into the root struct.

### 7. Long Method & Switch Statements — `loadLevelObstacles()` (UNTOUCHED)
- **Site:** `snake_game.cpp:304-366`
- **Confidence:** **C3 (High)** — Procedural generation of obstacles via `switch` statement (`snake_game.cpp:308-366`) remains unchanged.

### 8. Duplicate Code — Procedural UI Rendering (UNTOUCHED)
- **Site:** `snake_game.cpp:408-460`
- **Confidence:** **C3 (High)** — Manual ANSI rendering blocks remain unrefactored.

### 9. Primitive Obsession — Direction Handling (UNTOUCHED)
- **Site:** `snake_game.cpp:61`, `snake_game.cpp:230`
- **Confidence:** **C2 (Medium)** — Numeric casting and procedural functions for directions remain unencapsulated.
