# LLM-LOG

## 1. Setup

| Field | Details |
| :--- | :--- |
| **Model(s)** | Gemini 3.6 Flash (Medium) |
| **Tool / harness** | Antigravity Agent (Agentic Coding Assistant) |
| **IDE / editor** | Visual Studio Code / Antigravity |
| **Did you paste this assignment document into it?** | Yes. The exact requirements for "Part A" and the LLM logging format were pasted directly into the chat prompt. |

## 2. The Session

### General Information
- **Total number of prompts it took to get working code:** 2 prompts.
- **What code was given to it:** The agent was granted access to the entire workspace. It autonomously read `snake_game.cpp` (1064 lines) using its file-viewing tools to understand the existing logic and apply changes across the whole file.

### Prompts and Replies

#### Prompt 1
> "hello"

#### Reply 1
*The agent acknowledged the prompt and confirmed readiness to work on the Snake-Game repository.*

#### Prompt 2
> "1:Make it multiplayer
> Lab Assignment 3: Make it multiplayer — in someone else's code
> Problem Statement ... [Full assignment text]
> first explain what it is saying that how what will you change and do not run any git commands"

#### Reply 2
*The agent provided a detailed explanation of the assignment requirements and outlined the planned modifications across `snake_game.cpp` without executing any commands.*

#### Prompt 3
> "start implementing"

#### Reply 3 (Code Produced)
*The agent updated `snake_game.cpp` to introduce the second snake, WASD controls, dual score tracking, shared food eating, cross-snake collision detection, and loser announcements:*

```cpp
struct GameState {
    deque<Vec> snake;
    Dir dir = RIGHT;
    deque<Vec> snake2;
    Dir dir2 = LEFT;
    Vec food{-1, -1};
    // ...
    int score = 0, score2 = 0;
    string loserInfo = "";
};

static atomic<Dir> inputDir;
static atomic<Dir> inputDir2;

void inputThreadFunc() {
    // ...
    if (ch == 'w') inputDir2 = UP;
    else if (ch == 's') inputDir2 = DOWN;
    else if (ch == 'a') inputDir2 = LEFT;
    else if (ch == 'd') inputDir2 = RIGHT;
    else if (ch == currentKeys.up) inputDir = UP;
    // ...
}
```

#### Which attempt finally worked, and what was wrong with earlier ones?
The **first implementation attempt** (Prompt 3) yielded working code.
- **Prompt 1** was an initial greeting.
- **Prompt 2** provided the assignment requirements and requested an initial plan without modifying files.
- **Prompt 3** successfully produced the complete C++ implementation of the 2-player multiplayer Snake game in `snake_game.cpp`.
