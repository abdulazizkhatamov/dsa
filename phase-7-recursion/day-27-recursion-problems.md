# Day 27 — Recursion: Classic Problems

---

## Problem 1: Binary Search (Recursive)

You'll learn binary search properly in Phase 9, but recursion is a natural way to express it.

**Problem:** Find the index of a target in a sorted array. Return -1 if not found.

**Input:** `arr = [1, 3, 5, 7, 9, 11]`, `target = 7`
**Output:** `3`

**Recursive thinking:**
- Look at the middle element
- If it's the target → done
- If target is smaller → search the LEFT half
- If target is larger → search the RIGHT half

```javascript
function binarySearch(arr, target, left = 0, right = arr.length - 1) {
  // Base case: search space is empty
  if (left > right) return -1;

  const mid = Math.floor((left + right) / 2);

  if (arr[mid] === target) return mid;       // found it!
  if (arr[mid] > target) return binarySearch(arr, target, left, mid - 1);   // search left half
  return binarySearch(arr, target, mid + 1, right);  // search right half
}

console.log(binarySearch([1, 3, 5, 7, 9, 11], 7));   // 3
console.log(binarySearch([1, 3, 5, 7, 9, 11], 4));   // -1
console.log(binarySearch([1, 3, 5, 7, 9, 11], 1));   // 0
```

**Trace finding 7:**
```
binarySearch([1,3,5,7,9,11], 7, 0, 5)
  mid=2, arr[2]=5. 7>5 → search right
  binarySearch([...], 7, 3, 5)
    mid=4, arr[4]=9. 7<9 → search left
    binarySearch([...], 7, 3, 3)
      mid=3, arr[3]=7 === 7 → return 3 ✓
```

- Time: O(log n) — each call halves the search space
- Space: O(log n) — call stack depth (each level = one recursive call)

---

## Problem 2: Generate All Permutations

**Problem:** Given a string, return all possible orderings of its characters.

**Input:** `"abc"`
**Output:** `["abc", "acb", "bac", "bca", "cab", "cba"]`

For 3 characters, there are 3! = 6 permutations. For n characters: n! permutations.

**Recursive thinking:**
- Fix one character at the front
- Recursively generate permutations of the remaining characters
- Combine: fixed + each permutation of the rest

```javascript
function permutations(str) {
  // Base case: only one character — one permutation
  if (str.length <= 1) return [str];

  const result = [];

  for (let i = 0; i < str.length; i++) {
    const char = str[i];

    // All characters except the current one
    const remaining = str.slice(0, i) + str.slice(i + 1);

    // Get permutations of the remaining characters
    const permsOfRemaining = permutations(remaining);

    // Prepend current character to each permutation
    for (const perm of permsOfRemaining) {
      result.push(char + perm);
    }
  }

  return result;
}

console.log(permutations("abc"));
// ["abc","acb","bac","bca","cab","cba"]

console.log(permutations("ab"));
// ["ab","ba"]
```

**Trace for "abc":**
```
Fix 'a': permutations("bc") = ["bc","cb"] → "abc","acb"
Fix 'b': permutations("ac") = ["ac","ca"] → "bac","bca"
Fix 'c': permutations("ab") = ["ab","ba"] → "cab","cba"
```

- Time: O(n × n!) — n! permutations, each of length n
- Space: O(n × n!) — storing all permutations

---

## Problem 3: Flatten a Nested Array

**Problem:** Given a deeply nested array, return a flat array of all values.

**Input:** `[1, [2, [3, 4], 5], 6]`
**Output:** `[1, 2, 3, 4, 5, 6]`

**Recursive thinking:**
- For each element: if it's a number, add it to result
- If it's an array, recursively flatten it

```javascript
function flatten(arr) {
  const result = [];

  for (const item of arr) {
    if (Array.isArray(item)) {
      // Recursively flatten nested array
      result.push(...flatten(item));
    } else {
      result.push(item);
    }
  }

  return result;
}

console.log(flatten([1, [2, [3, 4], 5], 6]));  // [1, 2, 3, 4, 5, 6]
console.log(flatten([1, 2, 3]));                // [1, 2, 3] (already flat)
console.log(flatten([[[[1]]]]));                // [1]
```

---

## Problem 4: Flood Fill (Painting Algorithm)

**Problem:** Given a 2D grid of colors and a starting position, fill the connected region with a new color (like the paint bucket tool in image editors).

**Input:**
```
grid = [[1, 1, 1],
        [1, 1, 0],
        [1, 0, 1]]
start = row=1, col=1, newColor=2
```
**Output:**
```
[[2, 2, 2],
 [2, 2, 0],
 [2, 0, 1]]
```

**Recursive thinking:**
- Color the current cell
- Recursively flood fill all 4 neighbors (up, down, left, right) that have the original color

```javascript
function floodFill(grid, row, col, newColor) {
  const originalColor = grid[row][col];

  if (originalColor === newColor) return grid;  // already the target color, avoid infinite loop

  fill(grid, row, col, originalColor, newColor);
  return grid;
}

function fill(grid, row, col, originalColor, newColor) {
  // Base cases: out of bounds, or different color
  if (row < 0 || row >= grid.length) return;
  if (col < 0 || col >= grid[0].length) return;
  if (grid[row][col] !== originalColor) return;

  grid[row][col] = newColor;  // paint this cell

  // Recurse in all 4 directions
  fill(grid, row - 1, col, originalColor, newColor);  // up
  fill(grid, row + 1, col, originalColor, newColor);  // down
  fill(grid, row, col - 1, originalColor, newColor);  // left
  fill(grid, row, col + 1, originalColor, newColor);  // right
}

const grid = [[1,1,1],[1,1,0],[1,0,1]];
console.log(floodFill(grid, 1, 1, 2));
// [[2,2,2],[2,2,0],[2,0,1]]
```

This is your first preview of **DFS** (Depth-First Search) — a fundamental graph algorithm you'll learn properly in Phase 11.

---

## Problem 5: Tower of Hanoi

A classic recursion puzzle. You have 3 rods and n disks (smallest on top). Move all disks from rod A to rod C, using rod B as helper. Rules: only move one disk at a time, never put a larger disk on a smaller one.

```javascript
function hanoi(n, from, to, helper) {
  if (n === 1) {
    console.log(`Move disk 1 from ${from} to ${to}`);
    return;
  }

  // Step 1: move top (n-1) disks from `from` to `helper` (using `to`)
  hanoi(n - 1, from, helper, to);

  // Step 2: move the largest disk from `from` to `to`
  console.log(`Move disk ${n} from ${from} to ${to}`);

  // Step 3: move (n-1) disks from `helper` to `to` (using `from`)
  hanoi(n - 1, helper, to, from);
}

hanoi(3, 'A', 'C', 'B');
// Move disk 1 from A to C
// Move disk 2 from A to B
// Move disk 1 from C to B
// Move disk 3 from A to C
// Move disk 1 from B to A
// Move disk 2 from B to C
// Move disk 1 from A to C
```

Tower of Hanoi takes `2ⁿ - 1` moves. Big O: O(2ⁿ).

---

## The Mental Model for Recursion: "Trust the Recursion"

The hardest part of recursion is trusting that the recursive call will do its job correctly without you needing to trace through every step.

Example: for permutations, you don't need to trace through how `permutations("bc")` works. You just need to know: "It will correctly return all permutations of 'bc'." Then your job is just: take each of those, prepend your current character.

This is the **Leap of Faith** — assume the recursive call works, figure out what to do with its result.

---

## Practice Problems

**Practice 1:** Write a recursive function that checks if a string is a palindrome.
```
Input: "racecar" → true
Input: "hello"   → false
```

**Practice 2:** Write a recursive function to compute the power `x^n` (x to the power n).
```
Input: x=2, n=10 → 1024
Input: x=3, n=3  → 27
```

---

## Answers

**Practice 1:**
```javascript
function isPalindrome(s) {
  if (s.length <= 1) return true;                  // base case
  if (s[0] !== s[s.length - 1]) return false;      // outer chars don't match
  return isPalindrome(s.slice(1, -1));              // check inner substring
}

console.log(isPalindrome("racecar"));  // true
console.log(isPalindrome("hello"));    // false
```

**Practice 2:**
```javascript
function power(x, n) {
  if (n === 0) return 1;                    // base case: x⁰ = 1
  if (n % 2 === 0) {
    const half = power(x, n / 2);          // fast path: x^n = (x^(n/2))²
    return half * half;
  }
  return x * power(x, n - 1);
}

console.log(power(2, 10));  // 1024
console.log(power(3, 3));   // 27
```

The "fast path" optimization (called fast exponentiation) makes this O(log n) instead of O(n) — each even-power case halves the problem.

---

**Next: Day 28 — Recursion: Backtracking**
Backtracking is a recursive technique where you try all possibilities but abandon ("backtrack") paths that can't lead to a solution. Used for puzzles, combinations, and constraint-solving.
