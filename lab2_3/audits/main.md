# Code Smell Audit — Main Branch (`master`)

**Repository:** Snake-Game (`master` commit `d82a114`)  
**Target:** `snake_game.cpp` (1064 lines)  
**Evaluator:** Google Antigravity (using `detect-code-smells` and `review-accuracy-calibration`)

---

## 1. Summary of Identified Smells

| Smell Name | Category | File & Location | Confidence | Severity |
| --- | --- | --- | --- | --- |
| **Long Method** | Bloaters | `snake_game.cpp:823-997` | **C4** (Certain) | HIGH |
| **Dead Code** | Dispensables | `snake_game.cpp:63` | **C4** (Certain) | HIGH |
| **Dead Code** | Dispensables | `snake_game.cpp:134` | **C3** (High) | MEDIUM |
| **Large Class / Monolithic State** | Bloaters | `snake_game.cpp:118-135` | **C3** (High) | HIGH |
| **Global Mutable State** | Couplers | `snake_game.cpp:137-139` | **C3** (High) | HIGH |
| **Long Method** | Bloaters | `snake_game.cpp:298-360` | **C3** (High) | MEDIUM |
| **Switch Statements** | OO Abusers | `snake_game.cpp:302-360` | **C3** (High) | MEDIUM |
| **Duplicate Code** | Dispensables | `snake_game.cpp:402-454` | **C3** (High) | MEDIUM |
| **Primitive Obsession** | Bloaters | `snake_game.cpp:61,224` | **C2** (Medium) | LOW |

---

## 2. Detailed Smell Breakdown

### 1. Long Method — `runGame()`
- **Site:** `snake_game.cpp:823-997` (175 lines)
- **Confidence:** **C4 (Certain)** — Direct observation of a single loop handling 10 distinct responsibilities.
- **Description:** The core game loop manages frame timing, user input handling, speed calculations, dynamic obstacle spawning & updates, power-up spawning & timers, head movement, wall/obstacle collision checks, food eating & score updates, snake growth/shrinkage, cell rendering, and UI refresh all in a single monolithic function.

### 2. Dead Code / Feature Stub — `MAGNET` Power-Up
- **Site:** `snake_game.cpp:63`
- **Confidence:** **C4 (Certain)** — Code inspection confirms zero logic checking `activePowerUp == MAGNET`.
- **Description:** `MAGNET` is declared in `PowerUpType` (line 63), rendered with glyph `🧲` (line 88), and tracked in timers (lines 430-432), but no code branch in the game loop implements food attraction or magnet behavior when active.

### 3. Dead Code — Unpopulated `moveHistory`
- **Site:** `snake_game.cpp:134`
- **Confidence:** **C3 (High)** — `moveHistory` is declared in `GameState` and read in `replayGame()` (line 503), but no line in `runGame()` appends positions to `moveHistory`.
- **Description:** `GameState::moveHistory` is intended for game replays, but during gameplay it is never populated, rendering the replay feature non-functional.

### 4. Large Class / Monolithic Data Structure — `GameState` & `GameConfig`
- **Site:** `snake_game.cpp:118-135`
- **Confidence:** **C3 (High)** — Combines entity positions, scoring stats, active power-up timers, level state, and thread synchronization flags into a single unencapsulated struct.
- **Description:** `GameState` acts as a global dump for all runtime variables rather than separating domain models (Snake, Food, PowerUp) from game engine/system state.

### 5. Global Mutable State — Inter-Thread Statics
- **Site:** `snake_game.cpp:137-139`
- **Confidence:** **C3 (High)** — `static atomic<Dir> inputDir`, `static atomic<bool> inputQuit`, `static atomic<bool> inputPause`, and `static KeyBindings currentKeys` are global variables used for thread communication.
- **Description:** Inter-thread communication relies on global mutable static variables rather than passing thread contexts or message queues to `inputThreadFunc`.

### 6. Long Method & Switch Statements — `loadLevelObstacles()`
- **Site:** `snake_game.cpp:298-360`
- **Confidence:** **C3 (High)** — Procedural generation of obstacles using a raw `switch(level)`.
- **Description:** Level layouts are hardcoded inside a single 62-line function (`snake_game.cpp:298-360`) containing a 5-case `switch` statement (`snake_game.cpp:302-360`) rather than delegating level pattern creation to individual strategy classes or data-driven configurations.

### 7. Duplicate Code — Procedural UI Rendering
- **Site:** `snake_game.cpp:402-454`, `snake_game.cpp:461-480`, `snake_game.cpp:620-646`
- **Confidence:** **C3 (High)** — Repeated manual ANSI cursor positioning (`gotorc`) and text color formatting across multiple dialogs.
- **Description:** UI elements (Stats, Power-Ups, Controls, Settings, High Scores) repeatedly re-implement frame drawing and ANSI escape positioning manually instead of using a unified UI component abstraction.

### 8. Primitive Obsession — Direction Handling & Coordinates
- **Site:** `snake_game.cpp:61`, `snake_game.cpp:224-234`
- **Confidence:** **C2 (Medium)** — Direction logic uses primitive `enum Dir` cast to integers for random direction generation (`(Dir)urand(0,3)`).
- **Description:** Directional navigation and opposite-direction validation rely on procedural standalone functions (`isOpposite`, `step`) operating on raw values instead of encapsulating direction vectors into a proper value object.
