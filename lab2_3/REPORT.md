# Lab 2_3 — Group Aman Rohera

---

## 1. Tool and install route — [3]

| | |
|---|---|
| Agent used for run 2 | Google Antigravity |
| `ubiquitous-language` install route | workspace-scoped `.agents/skills/` inside the Snake-Game repo |
| `refactoring/` pack install route | workspace-scoped `.agents/skills/` inside the Snake-Game repo |

Both skills installed cleanly via Antigravity's native skill discovery (`.agents/skills/<name>/SKILL.md`), no paste fallback needed.

---

## 2. What I changed in the glossary — [4]

The generated file is at `lab2_3/UBIQUITOUS_LANGUAGE.md`. Three or four lines here on what
you corrected and why: terms it invented, definitions it got wrong, ambiguities it missed.

Removed the agent's invented example-dialogue section, since it wasn't grounded in the source. Merged the duplicate "flagged ambiguities" and "code drift" tables into one. Moved the Magnet finding out of the glossary — it's a missing-behavior issue, not a naming mismatch, so it now feeds into the Part C smell report instead. Verified all four remaining code-drift citations with grep before keeping them.

---

## 3. Smell delta — [6]

Reports: `lab2_3/audits/main.md` (the code as you received it) and `lab2_3/audits/lab1-head.md`
(after your Lab-1 PR).

| | count | representative site (`file:line`) |
|---|---|---|
| Smells my Lab-1 PR **introduced** | 1 | `snake_game.cpp:121` |
| Smells my Lab-1 PR **left untouched** | 8 | `snake_game.cpp:869` |
| Smells my Lab-1 PR **removed** | 0 | none |

The third row will probably be zero. Leave it in.

---

## 4. Rejected candidates — [6]

At least three things the agent reported that are *not* real findings on this codebase.

| smell reported | `file:line` | why it does not hold |
|---|---|---|
| Temporary Field | `snake_game.cpp:105` | `DynamicObstacle::speed` is set in `spawnDynamicObstacle` and actively used for frame throttling in `updateDynamicObstacles` (line 380); it is not dead state. |
| Speculative Generality | `snake_game.cpp:65-74` | `namespace Color` provides lightweight ANSI escape strings and helper functions (`bg`/`fg`) actively used throughout UI rendering without runtime overhead. |
| Feature Envy | `snake_game.cpp:197-226` | `calculateLayout()` reads `GameConfig` parameters (`cols`, `rows`) purely to position the terminal grid layout responsively without mutating internal state. |

---

## 5. Commit map — [7]

Run `lab2_3/check-lab2_3.sh` and paste the table it prints.

| # | sha | subject | what it is |
|---|---|---|---|
| 1 | e135e36 | docs: add ubiquitous language glossary | glossary |
| 2 | 70780ad | docs: add code smell audit for main branch | smell report |
| 3 | b55230f | refactor: generalize snake entity count into a player collection | the refactor alone |
| 4 | 257544b | feat: add local 2-player multiplayer snake mode | the feature alone |

---

## 6. Two-run measurement — [4]

Run 1 is your Lab-1 branch — the numbers you already reported. Run 2 is commit 4 alone.

| | Run 1 (Lab 1) | Run 2 (commit 4) |
|---|---|---|
| Smells introduced | 2 | 0 |
| Lines changed, `git diff --shortstat -w` | 5 files changed, 205 insertions(+), 64 deletions(-) | 1 file changed, 23 insertions(+), 16 deletions(-) |
| Lines changed, **raw** (no `-w`) | 5 files changed, 206 insertions(+), 65 deletions(-) | 1 file changed, 23 insertions(+), 16 deletions(-) |
| Functions reached | 8 | 2 |
| Prompts to working code | 4 | 1 |
| Wall-clock time | 25 mins | 5 mins |

Commit 3 (the refactor) on its own: 1 file changed, 127 insertions(+), 96 deletions(-) `-w`, 1 file changed, 173 insertions(+), 142 deletions(-) raw.

`check-lab2_3.sh` prints the four line-count numbers for run 2. Use them — they are measured
the same way for every group, which is what makes the class comparison mean anything.

---

## 7. Analysis Q1–Q2 — [5]

**Q1. Which smell did commit 3 actually fix?** Name it from your section 3 report. What was
expensive before, what does it cost now.

Commit 3 fixed **Duplicate Code** (and Primitive Obsession). Before refactoring, adding a second player required duplicating every snake operation (movement, wall bounds, obstacle collisions, food consumption, growth/shrinkage, and rendering) for `snake2`, duplicating score variables, and duplicating direction state across 8 functions (~270 lines changed). Now, using `Player` collection (`vector<Player> players`) and `numPlayers = 2` config in `GameConfig`, all snake operations run in a single loop. Adding Player 2 costs only 39 lines of code and zero duplicate logic.

**Q2. Compare commit 4 to your Lab-1 diff.** Same feature, same codebase. What changed in
the cost and what did not? If it got worse, say so and explain — that marks the same.

In Lab 1, adding multiplayer required 271 raw lines changed across 5 files, touching 8 functions, and introducing major Duplicate Code smells. In Commit 4, adding multiplayer required only 39 raw lines changed in 1 file (`snake_game.cpp`), touching only 2 functions (`inputThreadFunc` and `runGame`), with **0 code smells** introduced. Control wiring (WASD) and color glyph assignments remained necessary, but line churn dropped by ~85%, zero duplicate logic was added, and design health was preserved.

---

## 8. Analysis Q3–Q4 — [5]

**Q3. Go back through your Lab-1 `LLM-LOG.md`. Did the assistant ever suggest restructuring
before adding the feature?** Quote it if it did. If it did not, what would have had to be
different in your prompt?

No, the assistant did **not** suggest restructuring before adding the feature. In Reply 3 of `LLM-LOG.md`, when prompted to "start implementing", the assistant immediately added `deque<Vec> snake2`, `Dir dir2 = LEFT`, and `int score2 = 0` directly into `GameState` and duplicated game loop logic line-by-line. To make the assistant propose refactoring first, the prompt would need explicit design constraints, e.g.: *"Audit `GameState` for scalability before implementing. Refactor snake state into a reusable `Player` abstraction so player count is configured in a single place without duplicating fields or loops."*

**Q4. How do you know commit 3 did not change behaviour?** Answer honestly. Most of you will
find that you do not know. Say that plainly if it is true, and describe what you would have
needed in order to actually know.

We do **not** know with 100% mathematical certainty because the repo lacks automated tests. We verified behavior preservation manually by compiling (`g++ -std=c++17`) and playtesting single-player mode (movement, wall wrapping, obstacle collisions, food collection, scoring, level-ups). To *know* with certainty, we would need: (1) unit tests for core mechanics (`step()`, `inBounds()`), (2) deterministic game-state regression tests with recorded key sequences, and (3) automated CI test runs validating invariants before and after commit 3.

---