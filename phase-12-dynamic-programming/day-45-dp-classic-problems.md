# Day 45 — DP: Classic Problems

---

## Problem 1: Coin Change (Minimum Coins)

**Problem:** Given coins of certain denominations and a target amount, find the minimum number of coins needed to make that amount. Return -1 if impossible.

**Input:** `coins = [1, 5, 10, 25]`, `amount = 41`
**Output:** `4` (25 + 10 + 5 + 1)

**State:** `dp[i]` = minimum coins needed to make amount i
**Base case:** `dp[0] = 0` (0 coins to make amount 0)
**Transition:** For each amount, try every coin:
`dp[i] = min over all coins c where c <= i of (dp[i - c] + 1)`

```javascript
function coinChange(coins, amount) {
  const dp = new Array(amount + 1).fill(Infinity);
  dp[0] = 0;

  for (let i = 1; i <= amount; i++) {
    for (const coin of coins) {
      if (coin <= i) {
        dp[i] = Math.min(dp[i], dp[i - coin] + 1);
      }
    }
  }

  return dp[amount] === Infinity ? -1 : dp[amount];
}

console.log(coinChange([1, 5, 10, 25], 41));  // 4
console.log(coinChange([1, 5, 10], 11));       // 2 (10+1)
console.log(coinChange([2], 3));               // -1 (impossible)
```

**Building the table for `coins=[1,5]`, `amount=6`:**
```
dp[0] = 0

dp[1]:
  coin=1: dp[1-1]+1 = dp[0]+1 = 1
  dp[1] = 1

dp[2]:
  coin=1: dp[2-1]+1 = dp[1]+1 = 2
  dp[2] = 2

dp[3] = 3, dp[4] = 4

dp[5]:
  coin=1: dp[4]+1 = 5
  coin=5: dp[0]+1 = 1  ← better!
  dp[5] = 1

dp[6]:
  coin=1: dp[5]+1 = 2
  coin=5: dp[1]+1 = 2
  dp[6] = 2  (5+1 or 1+1+1+1+1+1... wait, coin=1 gives dp[5]+1=1+1=2)
```

- Time: O(amount × coins)
- Space: O(amount)

---

## Problem 2: Coin Change 2 (Number of Ways)

**Problem:** Count how many ways you can make `amount` using the coins.

**Input:** `coins = [1, 2, 5]`, `amount = 5`
**Output:** `4` (5=5, 5=2+2+1, 5=2+1+1+1, 5=1+1+1+1+1)

**State:** `dp[i]` = number of ways to make amount i
**Transition:** For each coin, add the ways to make `i - coin`

```javascript
function change(amount, coins) {
  const dp = new Array(amount + 1).fill(0);
  dp[0] = 1;  // one way to make 0: use no coins

  for (const coin of coins) {         // outer: each coin
    for (let i = coin; i <= amount; i++) {  // inner: each amount
      dp[i] += dp[i - coin];
    }
  }

  return dp[amount];
}

console.log(change(5, [1, 2, 5]));  // 4
console.log(change(3, [2]));        // 0
```

**Why coins on outside, amounts on inside?**
This ensures each combination is counted once (order doesn't matter). If we reversed the loops, `{1,2}` and `{2,1}` would be counted separately.

---

## Problem 3: 0/1 Knapsack

**Problem:** You have a bag with capacity W. There are n items, each with a weight and value. Choose items to maximize total value without exceeding capacity. You can take each item at most once.

**Input:**
```
weights = [2, 3, 4, 5]
values  = [3, 4, 5, 6]
capacity = 8
```
**Output:** `10` (take items with weight 3 and 5: value 4+6=10)

**State:** `dp[i][w]` = max value using first i items with capacity w
**Transition:**
- If we DON'T take item i: `dp[i][w] = dp[i-1][w]`
- If we DO take item i (only if weight[i] ≤ w): `dp[i][w] = dp[i-1][w - weight[i]] + value[i]`
- Take the max of both options

```javascript
function knapsack(weights, values, capacity) {
  const n = weights.length;
  // dp[i][w] = max value with first i items and capacity w
  const dp = Array.from({length: n + 1}, () => new Array(capacity + 1).fill(0));

  for (let i = 1; i <= n; i++) {
    const w = weights[i - 1];
    const v = values[i - 1];

    for (let c = 0; c <= capacity; c++) {
      dp[i][c] = dp[i-1][c];  // don't take item i

      if (w <= c) {
        dp[i][c] = Math.max(dp[i][c], dp[i-1][c - w] + v);  // take item i
      }
    }
  }

  return dp[n][capacity];
}

console.log(knapsack([2,3,4,5], [3,4,5,6], 8));  // 10
```

**The DP table (small example: weights=[2,3], values=[3,4], capacity=5):**
```
       0   1   2   3   4   5   (capacity)
i=0:  [0,  0,  0,  0,  0,  0]
i=1:  [0,  0,  3,  3,  3,  3]  (item 1: weight=2, value=3)
i=2:  [0,  0,  3,  4,  4,  7]  (item 2: weight=3, value=4)
                                 ↑ at capacity 5: took both (2+3=5 ≤ 5, value=3+4=7)
```

---

## Problem 4: Longest Increasing Subsequence (LIS)

**Problem:** Find the length of the longest subsequence where each element is strictly larger than the previous.

**Input:** `[10, 9, 2, 5, 3, 7, 101, 18]`
**Output:** `4` (subsequence: 2, 3, 7, 101 or 2, 5, 7, 101)

**State:** `dp[i]` = length of LIS ending at index i
**Transition:** `dp[i] = 1 + max(dp[j] for all j < i where arr[j] < arr[i])`

```javascript
function lengthOfLIS(nums) {
  const n = nums.length;
  const dp = new Array(n).fill(1);  // each element alone is a subsequence of length 1

  for (let i = 1; i < n; i++) {
    for (let j = 0; j < i; j++) {
      if (nums[j] < nums[i]) {
        dp[i] = Math.max(dp[i], dp[j] + 1);
      }
    }
  }

  return Math.max(...dp);
}

console.log(lengthOfLIS([10, 9, 2, 5, 3, 7, 101, 18]));  // 4
console.log(lengthOfLIS([0, 1, 0, 3, 2, 3]));            // 4
console.log(lengthOfLIS([7, 7, 7, 7]));                   // 1
```

- Time: O(n²)
- Space: O(n)

There's also an O(n log n) solution using binary search, but the O(n²) DP solution is more commonly expected in interviews.

---

## Summary: DP Problem Types

| Problem | State | Transition |
|---------|-------|-----------|
| Fibonacci/Climbing stairs | `dp[i]` = ways to reach step i | `dp[i] = dp[i-1] + dp[i-2]` |
| Min coins | `dp[i]` = min coins for amount i | `dp[i] = min(dp[i-coin]+1)` for each coin |
| Ways to make change | `dp[i]` = ways to make amount i | `dp[i] += dp[i-coin]` for each coin |
| 0/1 Knapsack | `dp[i][w]` = max value with i items, capacity w | Take or skip each item |
| LIS | `dp[i]` = LIS length ending at i | Check all previous smaller elements |

---

**Next: Day 46 — DP: String Problems**
Longest Common Subsequence, Edit Distance — two classic DP problems on strings.
