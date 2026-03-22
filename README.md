# 🎮 TicTac — Classic, Ultimate & Memory

> A single-file, zero-dependency web app featuring three variants of Tic Tac Toe with AI opponents, dark mode, sound effects, and full game state persistence.

![HTML](https://img.shields.io/badge/HTML5-E34F26?style=flat-square&logo=html5&logoColor=white)
![CSS](https://img.shields.io/badge/CSS3-1572B6?style=flat-square&logo=css3&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=flat-square&logo=javascript&logoColor=black)
![No Dependencies](https://img.shields.io/badge/dependencies-none-brightgreen?style=flat-square)
![License](https://img.shields.io/badge/license-MIT-blue?style=flat-square)

---

## ✨ Live Demo

Open `tictactoe.html` directly in any modern browser — no server, no build step, no install.

---

## 🕹️ Game Modes

### 🎯 Classic Tic Tac Toe
Standard 3×3 grid with one important enhancement: **deadlock draw detection**. The game doesn't wait for the board to fill — it continuously scans all 8 win-lines after every move. The moment no winning path exists for either player, the game is immediately called a draw and the impossible cells are visually dimmed.

### 🧠 Ultimate Tic Tac Toe
Nine 3×3 boards arranged in a 3×3 macro grid. Win three mini-boards in a row to claim the overall game. Unlike traditional Ultimate Tic Tac Toe, **all boards are always open** — you can play any empty cell on any un-won board on every turn, making strategy purely about macro positioning.

### 👁️ Memory Tic Tac Toe
Each player can hold at most **3 marks** on the board simultaneously. Placing a 4th mark causes your oldest mark to fade out before the new one appears. A ghost border warns you which mark will vanish before you commit. This creates an ever-shifting board where long-term spatial memory is the key skill.

---

## 🤖 AI System

Three difficulty tiers power the single-player mode across all game variants.

### Easy
Pure random move selection from all available cells. Good for young players or a quick warm-up.

### Medium
Rule-based heuristic agent:
1. **Win if possible** — scans every available move for an immediate win
2. **Block if necessary** — scans for opponent's immediate winning move and blocks it
3. **Prefer center** — takes cell 4 if available
4. **Prefer corners** — takes a random available corner (0, 2, 6, 8)
5. **Random fallback** — picks randomly from remaining cells

### Hard — Minimax with Alpha-Beta Pruning
The Hard AI uses the **Minimax algorithm** — a recursive game-tree search that assumes both players play optimally.

```
minimax(board, depth, isMaximizing, alpha, beta)
  if terminal state → return score
  if maximizing (AI's turn):
    for each move:
      score = minimax(board after move, depth+1, false, alpha, beta)
      alpha = max(alpha, score)
      prune if beta ≤ alpha
  if minimizing (human's turn):
    for each move:
      score = minimax(board after move, depth+1, true, alpha, beta)
      beta = min(beta, score)
      prune if beta ≤ alpha
```

**Scoring:** A terminal win for AI scores `+10 − depth` (faster wins score higher). A terminal loss scores `depth − 10` (slower losses score higher — harder to achieve). Draws score `0`.

**Alpha-Beta Pruning** cuts branches that can't possibly affect the final decision, reducing worst-case node evaluations from O(b^d) to roughly O(b^(d/2)).

**Depth cap at 7** prevents excessive computation while still playing near-optimally.

#### Ultimate Mode Hard AI
The Ultimate AI layers macro strategy on top of the cell-level minimax:
1. Find a move that wins a mini-board AND completes a macro win-line
2. Find a move that wins a mini-board AND blocks opponent's macro win-line
3. Find any move that wins a mini-board
4. Prefer macro-strategic board positions: center (board 4) → corners (0, 2, 6, 8) → edges

#### Memory Mode Hard AI
Simulates the vanishing of the oldest mark before evaluating each candidate move, so the AI reasons about the board as it will actually appear after placement — not as it currently stands.

---

## 🧩 Dead-Draw Detection (Classic Mode)

A standard Tic Tac Toe implementation calls a draw only when all 9 cells are filled. This app detects forced draws much earlier using **win-line viability analysis**:

```javascript
function deadDrawCheck(board) {
  // A line is "alive" only if it doesn't contain BOTH x and o
  const aliveLines = WIN_LINES.filter(([a, b, c]) => {
    const vals = [board[a], board[b], board[c]];
    return !(vals.includes('x') && vals.includes('o'));
  });
  // If zero alive lines remain, no one can ever win
  if (aliveLines.length === 0) return { isDead: true };
  ...
}
```

This is called after every move. Cells that exist only within dead lines are visually dimmed to show they are strategically irrelevant.

---

## 👁️ Memory Mode — How Vanishing Works

```
Player places 4th mark
  → oldest mark index saved
  → CSS 'fading' animation plays (680ms)
  → board state updated: oldest mark removed
  → new mark placed
  → win check runs on final board state
```

A ghost dashed border on the oldest mark acts as a pre-move warning. The AI accounts for vanishing when simulating future states.

---

## 🎨 UI & Design

- **Font:** Syne (display) + JetBrains Mono (labels/code elements)
- **Theme:** Dark-first with full light mode toggle, persisted to localStorage
- **Animations:** Cell pop on placement (cubic-bezier spring), fade-out for Memory mode, scale-in game-over overlay, staggered AI thinking dots
- **Score bar:** Live win counts per player with active-player highlight
- **Sound:** Web Audio API — distinct tones for move, vanish, win, and draw
- **Responsive:** Mobile-first layout, works from 320px up

---

## 💾 State Persistence

All game state is serialized to `localStorage` on every state change:

| Persisted field | Description |
|---|---|
| `cBoard`, `cCur`, `cStat` | Classic board, current player, game status |
| `uBoards`, `uBoardWin` | All 9 Ultimate boards and their winners |
| `mBoard`, `mQueue`, `mFading` | Memory board, mark order queue, fading index |
| `scores` | Win/loss/draw counters across sessions |
| `history` | Last 20 game results |
| `darkMode`, `soundOn` | UI preferences |

Refreshing or closing the tab restores the exact game state. If the AI was mid-move, it resumes its turn on reload.

---

## 📁 Project Structure

```
tictactoe.html          ← entire app (single file)
README.md
```

Everything — HTML, CSS, and JavaScript — lives in one file with no external dependencies beyond two Google Fonts.

---

## 🚀 Getting Started

```bash
git clone https://github.com/yourusername/tictac.git
cd tictac
open tictactoe.html     # macOS
# or just double-click the file on Windows/Linux
```

No npm, no build step, no backend. Just open and play.

---

## 🗺️ Roadmap

- [ ] LAN multiplayer (WebRTC or WebSocket)
- [ ] Animated winning line draw effect
- [ ] Ultimate mode: per-board dead-draw detection
- [ ] Accessibility improvements (keyboard nav, ARIA labels)
- [ ] PWA support (installable, offline)

---

## 📄 License

MIT — use it, fork it, ship it.
