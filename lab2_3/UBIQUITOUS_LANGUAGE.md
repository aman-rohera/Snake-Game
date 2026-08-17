# Ubiquitous Language

## Gameplay & Entities

| Term | Definition | Aliases to avoid | In code |
| --- | --- | --- | --- |
| **Snake** | A player-controlled entity consisting of a chain of connected grid segments that moves continuously across the board | Player, worm | `snake`, `snake2` — `snake_game.cpp:121,123` ⚠ |
| **Segment** | A single row/column coordinate occupied by a portion of a Snake's body or head | Body unit, block, cell | `Vec` — `snake_game.cpp:60` |
| **Apple** | An edible item spawned on the board that modifies a Snake's score and length upon collection | Food, item | `food`, `FoodType` — `snake_game.cpp:62,125` ⚠ |
| **Normal Apple** | A standard edible item that awards +10 points and increases Snake length by 1 segment | Regular food, red apple | `NORMAL` — `snake_game.cpp:62` |
| **Golden Apple** | A rare edible item that awards +50 points and increases Snake length by 2 segments | Bonus food, star | `GOLDEN` — `snake_game.cpp:62` |
| **Poison Apple** | A hazardous edible item that deducts 30 points and shrinks Snake length by 2 segments | Skull, negative food | `POISON` — `snake_game.cpp:62` |
| **Static Obstacle** | An immobile grid barrier loaded per level that destroys any non-invincible Snake upon impact | Block, barrier, rock | `obstacles` — `snake_game.cpp:127` |
| **Dynamic Obstacle** | A moving grid barrier that travels across the board and changes direction upon hitting boundaries or static obstacles | Moving block, hazard | `DynamicObstacle` — `snake_game.cpp:104` |

## Power-Ups

| Term | Definition | Aliases to avoid | In code |
| --- | --- | --- | --- |
| **Power-Up** | A temporary collectible buff spawned periodically on the board | Special item, perk | `PowerUp` — `snake_game.cpp:97` |
| **Slow Motion** | A Power-Up that temporarily reduces the overall game speed | Slow down, clock | `SLOW_MOTION` — `snake_game.cpp:63` |
| **Invincibility** | A Power-Up that grants temporary immunity to collision damage from obstacles and snakes | Shield, god mode | `INVINCIBILITY` — `snake_game.cpp:63` (`POWERUP_SHIELD` ⚠) |
| **Magnet** | A collectible Power-Up that is picked up, displayed, and timed like the others, but currently has no gameplay effect — see note below | Food magnet | `MAGNET` — `snake_game.cpp:63` ⚠ |

## Game Modes & Progression

| Term | Definition | Aliases to avoid | In code |
| --- | --- | --- | --- |
| **Wall Mode** | A game mode where board borders act as solid deadly boundaries | Hard border mode, standard mode | `bordersEnabled = true` — `snake_game.cpp:114` ⚠ |
| **Wrap Mode** | A game mode where a Snake passing through any board edge re-emerges on the opposite side | Borderless mode, pass-through mode | `bordersEnabled = false` — `snake_game.cpp:114` ⚠ |
| **Level** | A game stage defining specific obstacle patterns and baseline game speed | Stage, map | `currentLevel` — `snake_game.cpp:115` |
| **High Score** | A top-10 historical game record preserved in persistent storage | Leaderboard entry, record | `HighScore` — `snake_game.cpp:575` |

## Multiplayer & Controls

| Term | Definition | Aliases to avoid | In code |
| --- | --- | --- | --- |
| **Player 1 (P1)** | The primary player controlling the green Snake using directional arrow keys | Snake 1, P1 Snake | `snake`, `score` — `snake_game.cpp:121,133` ⚠ |
| **Player 2 (P2)** | The secondary player controlling the yellow Snake using WASD keys | Snake 2, P2 Snake | `snake2`, `score2` — `snake_game.cpp:123,133` ⚠ |
| **Head-on Collision** | A simultaneous move by both Player 1 and Player 2 into the exact same cell, eliminating both players | Double crash, tie crash | `loserInfo = "Head-on collision!"` — `snake_game.cpp:972` |

## Relationships

- A **Snake** consists of an ordered sequence of **Segments**
- Collecting an **Apple** changes a **Snake**'s score and total count of **Segments**
- A **Level** contains a set of **Static Obstacles** and may spawn **Dynamic Obstacles**
- A **Player** controls exactly one **Snake** during a multiplayer match
- A **High Score** record links a player name to their total score, **Snake** length, and reached **Level**

## Code drift

Places where the code's name for a concept is not the canonical term. Reported, not renamed.

| Canonical term | Called in code | Location | Note |
| --- | --- | --- | --- |
| **Apple** | `food` / `FoodType` | `snake_game.cpp:62,125` | Domain strictly specifies "Apples" (Normal, Golden, Poison); code uses generic "food" |
| **Invincibility** | `POWERUP_SHIELD` | `snake_game.cpp:89,431` | Code UI labels render "🛡 SHIELD" despite enum and domain calling it `INVINCIBILITY` |
| **Wall Mode / Wrap Mode** | `bordersEnabled` | `snake_game.cpp:114` | Code models mode as a boolean toggle rather than a domain `GameMode` concept |
| **Player 1 / Player 2** | `snake` / `snake2` | `snake_game.cpp:121,123` | Multiplayer entities are named `snake`/`snake2` and `score`/`score2` without a dedicated `Player` model |

## Note (not a naming ambiguity — flagged for Part C instead)

**Magnet has no gameplay effect.** Verified by tracing every use of `activePowerUp` in snake_game.cpp (lines 130, 430-432, 820, 847, 910, 953, 1002, 1012, 1056): SLOW_MOTION affects speed (line 847) and INVINCIBILITY affects collision/rendering (lines 910, 953, 1056), but no code branch checks for MAGNET.
