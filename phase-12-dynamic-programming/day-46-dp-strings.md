# Day 46 — DP: String Problems

---

## Problem 1: Longest Common Subsequence (LCS)

**Problem:** Given two strings, find the length of their longest common subsequence.

A subsequence is characters in order but NOT necessarily contiguous.
- "abcde" and "ace" → LCS = "ace", length 3
- "abc" and "abc" → LCS = "abc", length 3
- "abc" and "def" → LCS = "", length 0

**State:** `dp[i][j]` = LCS of `s1[0..i-1]` and `s2[0..j-1]`

**Transition:**
- If `s1[i-1] === s2[j-1]`: `dp[i][j] = dp[i-1][j-1] + 1` (characters match, extend)
- Otherwise: `dp[i][j] = max(dp[i-1][j], dp[i][j-1])` (skip one character from either)

```javascript
function lcs(s1, s2) {
  const m = s1.length;
  const n = s2.length;
  const dp = Array.from({length: m + 1}, () => new Array(n + 1).fill(0));

  for (let i = 1; i <= m; i++) {
    for (let j = 1; j <= n; j++) {
      if (s1[i-1] === s2[j-1]) {
        dp[i][j] = dp[i-1][j-1] + 1;  // characters match
      } else {
        dp[i][j] = Math.max(dp[i-1][j], dp[i][j-1]);  // skip one
      }
    }
  }

  return dp[m][n];
}

console.log(lcs("abcde", "ace"));   // 3
console.log(lcs("abc", "abc"));     // 3
console.log(lcs("abc", "def"));     // 0
```

**Building the table for "abcde" and "ace":**
```
     ""  a  c  e
""  [ 0, 0, 0, 0]
a   [ 0, 1, 1, 1]
b   [ 0, 1, 1, 1]
c   [ 0, 1, 2, 2]
d   [ 0, 1, 2, 2]
e   [ 0, 1, 2, 3]  ← answer is 3 ✓
```

- Time: O(m × n)
- Space: O(m × n)

---

## Problem 2: Edit Distance (Levenshtein Distance)

**Problem:** Find the minimum number of operations (insert, delete, replace) to convert string `s1` to `s2`.

**Input:** `s1 = "horse"`, `s2 = "ros"`
**Output:** `3`
- horse → rorse (replace h→r)
- rorse → rose (delete r)
- rose → ros (delete e)

**State:** `dp[i][j]` = min operations to convert `s1[0..i-1]` to `s2[0..j-1]`

**Transition:**
- If `s1[i-1] === s2[j-1]`: no operation needed, `dp[i][j] = dp[i-1][j-1]`
- Otherwise: take the minimum of:
  - `dp[i-1][j] + 1` (delete from s1)
  - `dp[i][j-1] + 1` (insert into s1)
  - `dp[i-1][j-1] + 1` (replace in s1)

```javascript
function editDistance(s1, s2) {
  const m = s1.length;
  const n = s2.length;
  const dp = Array.from({length: m + 1}, () => new Array(n + 1).fill(0));

  // Base cases: converting empty string
  for (let i = 0; i <= m; i++) dp[i][0] = i;  // delete all i characters
  for (let j = 0; j <= n; j++) dp[0][j] = j;  // insert all j characters

  for (let i = 1; i <= m; i++) {
    for (let j = 1; j <= n; j++) {
      if (s1[i-1] === s2[j-1]) {
        dp[i][j] = dp[i-1][j-1];  // no operation needed
      } else {
        dp[i][j] = 1 + Math.min(
          dp[i-1][j],    // delete
          dp[i][j-1],    // insert
          dp[i-1][j-1]   // replace
        );
      }
    }
  }

  return dp[m][n];
}

console.log(editDistance("horse", "ros"));  // 3
console.log(editDistance("intention", "execution"));  // 5
console.log(editDistance("", "abc"));  // 3
```

---

## Problem 3: Longest Palindromic Subsequence

**Problem:** Find the longest subsequence of a string that is a palindrome.

**Input:** `"bbbab"`
**Output:** `4` (subsequence "bbbb")

**Key insight:** LPS(s) = LCS(s, reverse(s))

```javascript
function longestPalindromicSubsequence(s) {
  return lcs(s, s.split('').reverse().join(''));
}

console.log(longestPalindromicSubsequence("bbbab"));  // 4
console.log(longestPalindromicSubsequence("cbbd"));   // 2
```

Or directly with DP:
```javascript
function lps(s) {
  const n = s.length;
  const dp = Array.from({length: n}, () => new Array(n).fill(0));

  // Every single character is a palindrome of length 1
  for (let i = 0; i < n; i++) dp[i][i] = 1;

  for (let len = 2; len <= n; len++) {
    for (let i = 0; i <= n - len; i++) {
      const j = i + len - 1;

      if (s[i] === s[j]) {
        dp[i][j] = dp[i+1][j-1] + 2;
      } else {
        dp[i][j] = Math.max(dp[i+1][j], dp[i][j-1]);
      }
    }
  }

  return dp[0][n-1];
}
```

---

## Pattern: 2D DP on Two Strings

Many string DP problems follow the same pattern:

```javascript
function twoStringDP(s1, s2) {
  const m = s1.length, n = s2.length;
  const dp = Array.from({length: m + 1}, () => new Array(n + 1).fill(BASE_VALUE));

  // Initialize base cases (first row and column)

  for (let i = 1; i <= m; i++) {
    for (let j = 1; j <= n; j++) {
      if (s1[i-1] === s2[j-1]) {
        dp[i][j] = dp[i-1][j-1] + MATCH_BONUS;
      } else {
        dp[i][j] = COMBINE(dp[i-1][j], dp[i][j-1], dp[i-1][j-1]);
      }
    }
  }

  return dp[m][n];
}
```

---

**Next: Day 47 — DP: Review and 2D Grid Problems**
Applying DP to 2D grids: paths in a grid, obstacles, and more.
