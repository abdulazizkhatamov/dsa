# Day 47 — DP: Grid Problems and Review

---

## DP on Grids

Many DP problems involve a 2D grid where you move from one corner to another, collecting maximum/minimum along the way.

**Standard grid DP setup:**
- You can only move RIGHT or DOWN
- `dp[r][c]` = best result from `[0,0]` to `[r,c]`
- Transition: `dp[r][c] = grid[r][c] + best(dp[r-1][c], dp[r][c-1])`

---

## Problem 1: Unique Paths

**Problem:** Count the number of unique paths from top-left to bottom-right, only moving right or down.

**Input:** `m=3, n=7`
**Output:** `28`

```javascript
function uniquePaths(m, n) {
  const dp = Array.from({length: m}, () => new Array(n).fill(1));
  // First row and column are all 1 (only one way to get there)

  for (let r = 1; r < m; r++) {
    for (let c = 1; c < n; c++) {
      dp[r][c] = dp[r-1][c] + dp[r][c-1];  // from above + from left
    }
  }

  return dp[m-1][n-1];
}

console.log(uniquePaths(3, 7));  // 28
console.log(uniquePaths(3, 2));  // 3
```

---

## Problem 2: Minimum Path Sum

**Problem:** Find the path from top-left to bottom-right with the minimum sum. Can only move right or down.

**Input:**
```
grid = [[1,3,1],
        [1,5,1],
        [4,2,1]]
```
**Output:** `7` (path: 1→3→1→1→1)

```javascript
function minPathSum(grid) {
  const m = grid.length;
  const n = grid[0].length;
  const dp = Array.from({length: m}, () => new Array(n).fill(0));

  dp[0][0] = grid[0][0];

  // First row (can only come from left)
  for (let c = 1; c < n; c++) {
    dp[0][c] = dp[0][c-1] + grid[0][c];
  }

  // First column (can only come from above)
  for (let r = 1; r < m; r++) {
    dp[r][0] = dp[r-1][0] + grid[r][0];
  }

  // Fill the rest
  for (let r = 1; r < m; r++) {
    for (let c = 1; c < n; c++) {
      dp[r][c] = grid[r][c] + Math.min(dp[r-1][c], dp[r][c-1]);
    }
  }

  return dp[m-1][n-1];
}

console.log(minPathSum([[1,3,1],[1,5,1],[4,2,1]]));  // 7
```

---

## Problem 3: Triangle (Variable Path DP)

**Problem:** Given a triangle array, find the minimum path sum from top to bottom. You can move to adjacent numbers on the row below.

```
Input:
[     [2],
    [3,4],
   [6,5,7],
  [4,1,8,3]]

Output: 11  (2→3→5→1)
```

```javascript
function minimumTotal(triangle) {
  // Start from second-to-last row and work upward
  const dp = [...triangle[triangle.length - 1]];

  for (let row = triangle.length - 2; row >= 0; row--) {
    for (let col = 0; col <= row; col++) {
      dp[col] = triangle[row][col] + Math.min(dp[col], dp[col + 1]);
    }
  }

  return dp[0];
}

console.log(minimumTotal([[2],[3,4],[6,5,7],[4,1,8,3]]));  // 11
```

---

## Phase 12 Self-Check

- [ ] What two properties make a problem suitable for DP?
- [ ] What is memoization? What is tabulation?
- [ ] What is the DP state for Fibonacci? For Coin Change?
- [ ] How do you handle the 0/1 knapsack? What does "0/1" mean?
- [ ] How do you compute LCS? What are the two cases in the transition?
- [ ] What is Edit Distance? What are the three operations?
- [ ] How do grid DP problems work? What's the transition?

---

## DP Problem Recognition Summary

| Keyword | Likely DP Type |
|---------|---------------|
| "How many ways" | Counting DP |
| "Minimum/maximum" | Optimization DP |
| "Can we achieve X" | Decision DP |
| "Subsequence" | LCS / LIS type |
| "Substring" | 2D string DP |
| "Path in grid" | 2D grid DP |
| "Choose items" | Knapsack type |

---

## All DP Patterns Recap

```
1D DP:
  - Fibonacci family: dp[i] = f(dp[i-1], dp[i-2])
  - Coin Change: dp[i] = min(dp[i-coin]+1) for each coin
  - LIS: dp[i] = 1 + max(dp[j] for j<i where arr[j]<arr[i])

2D DP (two strings):
  - LCS: match → diagonal+1, no match → max(left, up)
  - Edit Distance: match → diagonal, no match → 1+min(left,up,diagonal)

2D DP (grid):
  - Paths: dp[r][c] = dp[r-1][c] + dp[r][c-1]
  - Min path: dp[r][c] = grid[r][c] + min(dp[r-1][c], dp[r][c-1])

2D DP (items/capacity):
  - Knapsack: dp[i][w] = max(skip, take)
```

---

**Next: Day 48 — Advanced Patterns: Two Pointers Revisited**
Phase 13 begins. We revisit the patterns you've learned and solve harder problems that combine multiple techniques.
