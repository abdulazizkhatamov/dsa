# Day 6 — Big O: Every Common Complexity Explained

---

## The Speed Order (Best to Worst)

```
O(1)        → Best possible
O(log n)    → Excellent
O(n)        → Good
O(n log n)  → Decent
O(n²)       → Bad (avoid if possible)
O(2ⁿ)       → Very bad (almost never acceptable)
O(n!)       → Worst possible (basically never)
```

Let's go through each one. We'll use a real number — imagine n = 10 (10 items).

---

## O(1) — Constant Time

**What it means:** The code always takes the same number of steps, no matter how big the input is.

**Real life analogy:** Looking up a word in a dictionary if you already know the exact page number. It doesn't matter how thick the dictionary is — you go straight to that page.

```javascript
// These are all O(1):

// Get first element
function getFirst(arr) {
  return arr[0];  // always 1 step
}

// Add two numbers
function add(a, b) {
  return a + b;  // always 1 step
}

// Check if a number is even
function isEven(n) {
  return n % 2 === 0;  // always 1 step
}
```

Whether the array has 10 items or 10 million items — `arr[0]` always takes 1 step.

**When you see it:** Direct access by index, math operations, hash map lookups.

---

## O(log n) — Logarithmic Time

**What it means:** The code keeps cutting the problem in half. Each step eliminates half of the remaining possibilities.

**Real life analogy:** Searching a phonebook. You open to the middle, decide which half to look in, open to the middle of that half, and so on. Each step you cut the remaining pages in half.

```javascript
// Binary search is the classic O(log n) example
// (We'll learn binary search fully in Phase 9)

// Simple example: how many times can you halve a number before reaching 1?
function howManyHalves(n) {
  let steps = 0;
  while (n > 1) {
    n = Math.floor(n / 2);  // cut in half each step
    steps++;
  }
  return steps;
}

howManyHalves(8);    // 3  (8 → 4 → 2 → 1)
howManyHalves(16);   // 4  (16 → 8 → 4 → 2 → 1)
howManyHalves(1024); // 10 (cuts 10 times)
howManyHalves(1000000); // ~20
```

**With n = 10:** about 3-4 steps
**With n = 1,000,000:** about 20 steps
**With n = 1,000,000,000:** about 30 steps

Incredibly fast for large inputs.

**When you see it:** Binary search, traversing a balanced tree.

---

## O(n) — Linear Time

**What it means:** The number of steps grows directly with the input size. Double the input → double the steps.

**Real life analogy:** Reading every page of a book to find a specific sentence. 100-page book = 100 pages to check. 200-page book = 200 pages to check.

```javascript
// Looping through an array once = O(n)

function findMax(arr) {
  let max = arr[0];

  for (let i = 0; i < arr.length; i++) {  // visits each element once
    if (arr[i] > max) {
      max = arr[i];
    }
  }

  return max;
}

// sum all numbers
function sum(arr) {
  let total = 0;
  for (let i = 0; i < arr.length; i++) {  // each element once
    total += arr[i];
  }
  return total;
}

// print every element
function printAll(arr) {
  for (const item of arr) {  // each element once
    console.log(item);
  }
}
```

**With n = 10:** 10 steps
**With n = 1,000:** 1,000 steps
**With n = 1,000,000:** 1,000,000 steps

Grows steadily, but in a predictable and manageable way.

**When you see it:** A single loop through the data.

---

## O(n log n) — "n log n" Time

**What it means:** Slightly worse than linear. Most efficient sorting algorithms are O(n log n).

**Real life analogy:** Organizing a deck of cards using the "split in half and merge" strategy. You need to look at each card (n) but do it in a smart half-splitting way (log n).

```javascript
// JavaScript's built-in sort is O(n log n)
[3, 1, 4, 1, 5, 9, 2, 6].sort((a, b) => a - b);

// Merge sort and quick sort are also O(n log n) — we'll learn these in Phase 8
```

**With n = 10:** ~33 steps (10 × log₂(10) ≈ 10 × 3.3)
**With n = 1,000:** ~10,000 steps
**With n = 1,000,000:** ~20,000,000 steps

Still very good. This is the best we can do for general sorting.

**When you see it:** Efficient sorting algorithms (merge sort, quick sort).

---

## O(n²) — Quadratic Time

**What it means:** A loop inside a loop. For every item, you do work for every other item.

**Real life analogy:** A handshake problem. If everyone in a room shakes hands with everyone else: 5 people = 10 handshakes, 10 people = 45 handshakes, 100 people = 4,950 handshakes. It grows fast.

```javascript
// A nested loop = O(n²)

// Print every pair of elements
function printPairs(arr) {
  for (let i = 0; i < arr.length; i++) {      // outer loop: n times
    for (let j = 0; j < arr.length; j++) {    // inner loop: n times for EACH i
      console.log(arr[i], arr[j]);
    }
  }
}

// Bubble sort (the slow sorting algorithm) is O(n²)
function bubbleSort(arr) {
  for (let i = 0; i < arr.length; i++) {
    for (let j = 0; j < arr.length - 1; j++) {
      if (arr[j] > arr[j + 1]) {
        // swap
        let temp = arr[j];
        arr[j] = arr[j + 1];
        arr[j + 1] = temp;
      }
    }
  }
  return arr;
}
```

**With n = 10:** 100 steps
**With n = 1,000:** 1,000,000 steps (one million!)
**With n = 10,000:** 100,000,000 steps (one hundred million — very slow)

**When you see it:** Nested loops. Simple (but slow) sorting algorithms. Avoid for large inputs.

---

## O(2ⁿ) — Exponential Time

**What it means:** Each additional item DOUBLES the number of steps. Remember Day 2 — exponents grow insanely fast.

**Real life analogy:** Trying every possible combination to open a lock. A 10-digit lock has 2¹⁰ = 1,024 combinations. A 30-digit lock has 2³⁰ = over a billion combinations.

```javascript
// The naive fibonacci calculation is O(2ⁿ)
// (We'll see why when we learn recursion)

function fib(n) {
  if (n <= 1) return n;
  return fib(n - 1) + fib(n - 2);  // calls itself TWICE each time!
}

// fib(5)  → ~30 steps
// fib(10) → ~1,000 steps
// fib(30) → ~2,000,000,000 steps (2 billion!)
// fib(50) → would take years
```

**When you see it:** Naive recursive solutions. Brute-force "try all possibilities" approaches. You should almost always optimize this away.

---

## Side-by-Side Comparison

| n (input size) | O(1) | O(log n) | O(n) | O(n log n) | O(n²) | O(2ⁿ) |
|----------------|------|----------|------|------------|-------|-------|
| 1 | 1 | 0 | 1 | 0 | 1 | 2 |
| 10 | 1 | 3 | 10 | 33 | 100 | 1,024 |
| 100 | 1 | 7 | 100 | 664 | 10,000 | way too big |
| 1,000 | 1 | 10 | 1,000 | 10,000 | 1,000,000 | impossible |
| 1,000,000 | 1 | 20 | 1,000,000 | 20,000,000 | 10¹² | literally can't compute |

---

## How to Quickly Identify Big O in Code

Look for the pattern:

```javascript
// Just looking up or doing one thing → O(1)
arr[0]
map.get(key)
a + b

// One loop through data → O(n)
for (let i = 0; i < n; i++) { ... }
arr.forEach(...)
arr.map(...)

// Loop inside a loop (both based on n) → O(n²)
for (let i = 0; i < n; i++) {
  for (let j = 0; j < n; j++) { ... }
}

// Repeatedly halving → O(log n)
while (n > 1) { n = Math.floor(n / 2); }
```

---

## Summary

| Complexity | Name | Real Example |
|-----------|------|-------------|
| O(1) | Constant | Access array by index |
| O(log n) | Logarithmic | Binary search |
| O(n) | Linear | Print all elements |
| O(n log n) | n log n | Efficient sorting (merge sort) |
| O(n²) | Quadratic | Nested loops, bubble sort |
| O(2ⁿ) | Exponential | Naive fibonacci, "try all combinations" |

---

**Next: Day 7 — Big O Practice**
Reading real code and figuring out its Big O. This is what interviews test.
