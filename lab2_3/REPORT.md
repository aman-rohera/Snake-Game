# Lab 2_3 — Group A__

> Copy this file to `lab2_3/REPORT.md` in your fork and fill it in. Keep the eight
> headings exactly as they are — they are the marking scheme, in order. Delete this
> quote block and every `___` when you are done.

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
| 1 | ___ | ___ | glossary |
| 2 | ___ | ___ | smell report |
| 3 | ___ | ___ | **the refactor, alone** |
| 4 | ___ | ___ | **the feature, alone** |

---

## 6. Two-run measurement — [4]

Run 1 is your Lab-1 branch — the numbers you already reported. Run 2 is commit 4 alone.

| | Run 1 (Lab 1) | Run 2 (commit 4) |
|---|---|---|
| Smells introduced | ___ | ___ |
| Lines changed, `git diff --shortstat -w` | ___ | ___ |
| Lines changed, **raw** (no `-w`) | ___ | ___ |
| Functions reached | ___ | ___ |
| Prompts to working code | ___ | ___ |
| Wall-clock time | ___ | ___ |

Commit 3 (the refactor) on its own: ___ lines `-w`, ___ raw.

`check-lab2_3.sh` prints the four line-count numbers for run 2. Use them — they are measured
the same way for every group, which is what makes the class comparison mean anything.

---

## 7. Analysis Q1–Q2 — [5]

**Q1. Which smell did commit 3 actually fix?** Name it from your section 3 report. What was
expensive before, what does it cost now.

___

**Q2. Compare commit 4 to your Lab-1 diff.** Same feature, same codebase. What changed in
the cost and what did not? If it got worse, say so and explain — that marks the same.

___

---

## 8. Analysis Q3–Q4 — [5]

**Q3. Go back through your Lab-1 `LLM-LOG.md`. Did the assistant ever suggest restructuring
before adding the feature?** Quote it if it did. If it did not, what would have had to be
different in your prompt?

___

**Q4. How do you know commit 3 did not change behaviour?** Answer honestly. Most of you will
find that you do not know. Say that plainly if it is true, and describe what you would have
needed in order to actually know.

___

---

Sections 7 and 8 together: **500 words maximum.**

## If you did not finish

Say so here and lose no marks. What you got to, where it broke, what you tried.

___
