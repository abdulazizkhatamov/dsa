# Day 44 — Dynamic Programming: Introduction

---

## What is Dynamic Programming?

Dynamic Programming (DP) solves problems that have two properties:
1. **Overlapping subproblems** — the same smaller problems are solved repeatedly
2. **Optimal substructure** — the best solution to the whole problem is built from best solutions to subproblems

You already saw this problem in recursion:

```javascript
// Fibonacci — exponential without DP
function fib(n) {
  if (n <= 1) return n;
  return fib(n-1) + fib(n-2);
}
```

`fib(5)` calls `fib(4)` and `fib(3)`. `fib(4)` calls `fib(3)` and `fib(2)`. `fib(3)` is computed TWICE.

For large n, these repeated computations explode exponentially.

**DP solution:** Remember (cache) the result the first time. Never compute the same thing twice.

---

## Two Approaches to DP

### Approach 1: Memoization (Top-Down)

Start with the recursive solution. Add a cache (usually a Map or object). Before computing, check the cache. After computing, store in cache.

```javascript
function fib(n, memo = {}) {
  if (n in memo) return memo[n];  // already computed!
  if (n <= 1) return n;

  memo[n] = fib(n-1, memo) + fib(n-2, memo);  // compute and store
  return memo[n];
}

console.log(fib(50));  // 12586269025 — instant, not exponential
```

- Start with the big problem
- Recursively break down
- Cache results as you go

### Approach 2: Tabulation (Bottom-Up)

Start from the smallest subproblems. Build up to the answer iteratively. No recursion.

```javascript
function fib(n) {
  if (n <= 1) return n;

  const dp = new Array(n + 1);
  dp[0] = 0;
  dp[1] = 1;

  for (let i = 2; i <= n; i++) {
    dp[i] = dp[i-1] + dp[i-2];  // use previously computed values
  }

  return dp[n];
}

console.log(fib(50));  // 12586269025
```

- Start from base cases
- Build up in a table
- No call stack

Both give O(n) time and O(n) space for Fibonacci.

**Space optimization:** If you only need the last two values:
```javascript
function fib(n) {
  if (n <= 1) return n;
  let prev = 0, curr = 1;
  for (let i = 2; i <= n; i++) {
    [prev, curr] = [curr, prev + curr];
  }
  return curr;
}
```
O(n) time, **O(1) space**.

---

## The DP Thought Process

When you see a problem, ask:
1. **Can I define the problem recursively?** (what's the smallest version?)
2. **Are there overlapping subproblems?** (same smaller problems repeated?)
3. **What is the state?** (what information defines each subproblem?)
4. **What is the transition?** (how does a bigger problem use smaller ones?)

---

## Problem 1: Climbing Stairs

**Problem:** You can climb 1 or 2 steps at a time. How many ways can you reach step n?

**Input:** n=3
**Output:** 3 (1+1+1, 1+2, 2+1)

**Observation:** To reach step n, you must have come from step n-1 (one step) or step n-2 (two steps).

So: `ways(n) = ways(n-1) + ways(n-2)`

This is EXACTLY Fibonacci!

```javascript
function climbStairs(n) {
  if (n <= 2) return n;

  let prev = 1, curr = 2;
  for (let i = 3; i <= n; i++) {
    [prev, curr] = [curr, prev + curr];
  }
  return curr;
}

console.log(climbStairs(2));  // 2
console.log(climbStairs(3));  // 3
console.log(climbStairs(5));  // 8
```

---

## Problem 2: Min Cost Climbing Stairs

**Problem:** Each step has a cost. You can climb 1 or 2 steps. Find the minimum total cost to reach the top.

**Input:** `cost = [10, 15, 20]`
**Output:** `15` (start at index 1, jump 2 steps to top: cost 15)

**State:** `dp[i]` = minimum cost to reach step i
**Transition:** `dp[i] = cost[i] + min(dp[i-1], dp[i-2])`

```javascript
function minCostClimbingStairs(cost) {
  const n = cost.length;
  const dp = new Array(n + 1);

  dp[0] = 0;  // start before first step, free
  dp[1] = 0;  // can also start at index 1, free

  for (let i = 2; i <= n; i++) {
    dp[i] = Math.min(
      dp[i-1] + cost[i-1],  // came from step i-1
      dp[i-2] + cost[i-2]   // came from step i-2
    );
  }

  return dp[n];  // reach the top (after all steps)
}

console.log(minCostClimbingStairs([10, 15, 20]));  // 15
console.log(minCostClimbingStairs([1, 100, 1, 1, 1, 100, 1, 1, 100, 1]));  // 6
```

---

## The DP Table: Visualizing Solutions

For tabulation problems, draw the table:

```
cost = [10, 15, 20], top is index 3

index:  0   1   2   3  (3 = top, past all steps)
dp:    [0,  0,  ?,  ?]

dp[2] = min(dp[1] + cost[1], dp[0] + cost[0])
      = min(0 + 15, 0 + 10)
      = 10

dp[3] = min(dp[2] + cost[2], dp[1] + cost[1])
      = min(10 + 20, 0 + 15)
      = min(30, 15)
      = 15 ✓
```

---

## Identifying DP Problems

DP problems often have these keywords:
- "How many ways..."
- "What is the maximum/minimum..."
- "Can you reach..."
- "Is it possible..."
- "Find the optimal..."

And these structural hints:
- Decision at each step (take or don't take)
- Path through a grid
- Substrings or subsequences
- Partitioning problems

---

## Summary

| Approach | Direction | Uses | Pros |
|----------|-----------|------|------|
| Memoization | Top-down (recursive) | Cache in Map/object | Natural to write, only computes needed subproblems |
| Tabulation | Bottom-up (iterative) | Array/table | No call stack, easy to optimize space |

**The key insight:** If you find yourself computing the same recursive call more than once → DP can help.

---

**Next: Day 45 — DP: Classic Problems (Knapsack, Coin Change)**
The most famous DP problems that appear in almost every interview.
