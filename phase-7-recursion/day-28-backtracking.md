# Day 28 — Backtracking

---

## What is Backtracking?

Backtracking is a technique where you:
1. Build a solution step by step
2. If a step leads to a dead end → **undo that step** (backtrack)
3. Try the next option

It's like navigating a maze:
- You walk down a path
- Hit a wall → go back to the last junction
- Try a different path

Without backtracking, you'd have to explore ALL combinations (exponential). With backtracking, you **prune** paths that can't possibly lead to a solution early.

---

## The Backtracking Template

```javascript
function backtrack(path, choices) {
  // Base case: we've built a complete solution
  if (isDone(path)) {
    result.push([...path]);  // save current solution (use spread to copy!)
    return;
  }

  for (const choice of choices) {
    if (isValid(choice, path)) {       // only explore valid choices
      path.push(choice);               // CHOOSE: add to path
      backtrack(path, nextChoices);    // EXPLORE: recurse deeper
      path.pop();                      // UNCHOOSE: remove it (backtrack)
    }
  }
}
```

The pattern is always: **Choose → Explore → Unchoose**

---

## Problem 1: Generate All Subsets

**Problem:** Given an array of distinct integers, return all possible subsets (including empty set).

**Input:** `[1, 2, 3]`
**Output:** `[[], [1], [2], [1,2], [3], [1,3], [2,3], [1,2,3]]`

For n elements: 2ⁿ subsets (each element is either IN or NOT IN).

```javascript
function subsets(nums) {
  const result = [];

  function backtrack(start, current) {
    result.push([...current]);  // every state is a valid subset

    for (let i = start; i < nums.length; i++) {
      current.push(nums[i]);             // CHOOSE
      backtrack(i + 1, current);         // EXPLORE (only go forward, no repeats)
      current.pop();                     // UNCHOOSE (backtrack)
    }
  }

  backtrack(0, []);
  return result;
}

console.log(subsets([1, 2, 3]));
// [[], [1], [1,2], [1,2,3], [1,3], [2], [2,3], [3]]
```

**Trace:**
```
backtrack(0, []):
  save []
  i=0: push 1 → backtrack(1, [1])
    save [1]
    i=1: push 2 → backtrack(2, [1,2])
      save [1,2]
      i=2: push 3 → backtrack(3, [1,2,3])
        save [1,2,3]
        no more i → return
      pop 3 → [1,2]
    pop 2 → [1]
    i=2: push 3 → backtrack(3, [1,3])
      save [1,3]
    pop 3 → [1]
  pop 1 → []
  i=1: push 2 → ... (similar)
  i=2: push 3 → ... (similar)
```

---

## Problem 2: Generate All Combinations

**Problem:** Given `n` and `k`, return all combinations of `k` numbers from 1 to n.

**Input:** `n=4, k=2`
**Output:** `[[1,2],[1,3],[1,4],[2,3],[2,4],[3,4]]`

```javascript
function combinations(n, k) {
  const result = [];

  function backtrack(start, current) {
    if (current.length === k) {
      result.push([...current]);
      return;
    }

    for (let i = start; i <= n; i++) {
      current.push(i);
      backtrack(i + 1, current);
      current.pop();
    }
  }

  backtrack(1, []);
  return result;
}

console.log(combinations(4, 2));
// [[1,2],[1,3],[1,4],[2,3],[2,4],[3,4]]
```

---

## Problem 3: N-Queens

**Problem:** Place N queens on an N×N chessboard so no two queens threaten each other. Queens attack in all 8 directions. Return all valid arrangements.

**Input:** `n=4`
**Output:** Two solutions:
```
.Q..    ..Q.
...Q    Q...
Q...    ...Q
..Q.    .Q..
```

Each row must have exactly one queen. So we try placing a queen in each column of each row, and backtrack if two queens threaten each other.

```javascript
function solveNQueens(n) {
  const result = [];
  const queens = new Array(n).fill(-1);  // queens[row] = column of queen in that row

  function isValid(row, col) {
    for (let r = 0; r < row; r++) {
      const c = queens[r];
      if (c === col) return false;                    // same column
      if (Math.abs(r - row) === Math.abs(c - col)) return false;  // same diagonal
    }
    return true;
  }

  function backtrack(row) {
    if (row === n) {
      // Build board representation
      const board = queens.map(col => {
        return '.'.repeat(col) + 'Q' + '.'.repeat(n - col - 1);
      });
      result.push(board);
      return;
    }

    for (let col = 0; col < n; col++) {
      if (isValid(row, col)) {
        queens[row] = col;    // CHOOSE
        backtrack(row + 1);   // EXPLORE
        queens[row] = -1;     // UNCHOOSE
      }
    }
  }

  backtrack(0);
  return result;
}

console.log(solveNQueens(4).length);  // 2 solutions
console.log(solveNQueens(4)[0]);      // [".Q..", "...Q", "Q...", "..Q."]
```

---

## Problem 4: Word Search

**Problem:** Given a 2D grid of characters, determine if the word exists in the grid (connected horizontally or vertically).

**Input:**
```
board = [["A","B","C","E"],
         ["S","F","C","S"],
         ["A","D","E","E"]]
word = "ABCCED"
```
**Output:** `true` (A→B→C→C→E→D)

```javascript
function wordSearch(board, word) {
  const rows = board.length;
  const cols = board[0].length;

  function backtrack(row, col, index) {
    // Base case: we've matched all characters
    if (index === word.length) return true;

    // Out of bounds or wrong character
    if (row < 0 || row >= rows) return false;
    if (col < 0 || col >= cols) return false;
    if (board[row][col] !== word[index]) return false;

    // Mark as visited (temporarily)
    const temp = board[row][col];
    board[row][col] = '#';

    // Explore all 4 directions
    const found =
      backtrack(row + 1, col, index + 1) ||
      backtrack(row - 1, col, index + 1) ||
      backtrack(row, col + 1, index + 1) ||
      backtrack(row, col - 1, index + 1);

    // Unmark (backtrack)
    board[row][col] = temp;

    return found;
  }

  // Try starting from every cell
  for (let r = 0; r < rows; r++) {
    for (let c = 0; c < cols; c++) {
      if (backtrack(r, c, 0)) return true;
    }
  }

  return false;
}
```

The `board[row][col] = '#'` is the "visited" marker — it prevents revisiting the same cell in one path. After exploring, we restore it (backtrack).

---

## When to Use Backtracking

| Problem Type | Example |
|-------------|---------|
| Generate all combinations/subsets | "Find all subsets of [1,2,3]" |
| Generate all permutations | "Find all orderings of 'abc'" |
| Constraint satisfaction | N-Queens, Sudoku |
| Path finding in grid | Word Search, Maze solving |
| "Find all ways to..." | Coin change combinations |

If you see: **"find ALL possible..."** or **"generate all..."** → think backtracking.

---

## Summary

Backtracking = recursion + undoing choices

The pattern every time:
1. **Choose** — add something to your current path
2. **Explore** — recurse deeper
3. **Unchoose** — remove it and try the next option

The key to efficiency: check constraints EARLY to prune impossible paths before going deeper.

---

## Practice Problem

**Practice:** Generate all valid combinations of phone number digits (like a phone keypad).

```
digits: "23"
mapping: 2→"abc", 3→"def"
Output: ["ad","ae","af","bd","be","bf","cd","ce","cf"]
```

```javascript
const phoneMap = {
  "2": "abc", "3": "def", "4": "ghi", "5": "jkl",
  "6": "mno", "7": "pqrs", "8": "tuv", "9": "wxyz"
};

function letterCombinations(digits) {
  if (!digits) return [];
  const result = [];

  function backtrack(index, current) {
    if (index === digits.length) {
      result.push(current);
      return;
    }

    for (const letter of phoneMap[digits[index]]) {
      backtrack(index + 1, current + letter);
    }
  }

  backtrack(0, "");
  return result;
}

console.log(letterCombinations("23"));
// ["ad","ae","af","bd","be","bf","cd","ce","cf"]
```

Notice: strings are immutable, so `current + letter` creates a new string each time — no need to "undo" like we do with arrays. The backtracking is implicit.

---

**Next: Day 29 — Sorting: Bubble Sort and Selection Sort**
Phase 8 begins. You'll learn how sorting works from the ground up — starting with simple (but slow) algorithms, then learning why the faster ones are faster.
