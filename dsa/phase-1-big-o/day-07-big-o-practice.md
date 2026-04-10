# Day 7 — Big O Practice: Read Code, Find the Complexity

---

## How to Approach This

When you look at a piece of code, follow these steps:

1. Find the loops
2. Are they nested? (loop inside loop)
3. Does any part cut the problem in half each time?
4. Drop constants and smaller terms
5. State the worst case

Let's practice this with real examples.

---

## Example 1

```javascript
function example1(n) {
  let result = 0;
  result = n * 2;        // step 1
  result = result + 10;  // step 2
  return result;         // step 3
}
```

**Analysis:**
- 3 steps, no loops
- Always 3 steps, regardless of what n is
- Drop the constant (3)

**Answer: O(1)**

---

## Example 2

```javascript
function example2(arr) {
  let sum = 0;

  for (let i = 0; i < arr.length; i++) {
    sum += arr[i];
  }

  return sum;
}
```

**Analysis:**
- One loop
- Loop runs `arr.length` times (n times)
- Each iteration does 1 step (sum +=)

**Answer: O(n)**

---

## Example 3

```javascript
function example3(arr) {
  for (let i = 0; i < arr.length; i++) {
    console.log(arr[i]);          // loop 1
  }

  for (let i = 0; i < arr.length; i++) {
    console.log(arr[i] * 2);      // loop 2
  }
}
```

**Analysis:**
- Two separate loops (NOT nested)
- Each runs n times
- Total: n + n = 2n steps
- Drop the constant (2)

**Answer: O(n)**

Why not O(2n)? Because constants don't matter. Whether you loop twice or once, if n grows big enough, both are "linear growth". We call both O(n).

---

## Example 4

```javascript
function example4(arr) {
  for (let i = 0; i < arr.length; i++) {
    for (let j = 0; j < arr.length; j++) {
      console.log(arr[i], arr[j]);
    }
  }
}
```

**Analysis:**
- Nested loops
- Outer loop: n times
- Inner loop: n times for EACH outer iteration
- Total: n × n = n²

**Answer: O(n²)**

---

## Example 5

```javascript
function example5(arr) {
  for (let i = 0; i < arr.length; i++) {        // runs n times
    for (let j = 0; j < arr.length; j++) {      // runs n times
      console.log(arr[i], arr[j]);
    }
  }

  for (let k = 0; k < arr.length; k++) {        // runs n times
    console.log(arr[k]);
  }
}
```

**Analysis:**
- Nested loop: n²
- Single loop: n
- Total: n² + n
- Which dominates? When n is large, n² is MUCH bigger than n
- Drop the smaller term (n)

**Answer: O(n²)**

---

## Example 6

```javascript
function example6(arr1, arr2) {
  for (let i = 0; i < arr1.length; i++) {
    for (let j = 0; j < arr2.length; j++) {
      if (arr1[i] === arr2[j]) {
        console.log("match!");
      }
    }
  }
}
```

**Important! Two different arrays!**

**Analysis:**
- arr1 has `n` items
- arr2 has `m` items (a different variable!)
- Outer loop: n times
- Inner loop: m times for each outer

**Answer: O(n × m)** — NOT O(n²)

We use different letters because the arrays could have different sizes. If they were guaranteed to be the same size, we'd say O(n²). But here they're independent.

---

## Example 7

```javascript
function example7(n) {
  while (n > 1) {
    n = Math.floor(n / 2);
    console.log(n);
  }
}
```

**Analysis:**
- The loop keeps halving n
- Starts at n, goes n/2, n/4, n/8... until reaching 1
- How many times can you halve before reaching 1? log₂(n) times
- Remember from Day 3: that's what logarithm means!

**Answer: O(log n)**

---

## Example 8

```javascript
function example8(arr) {
  for (let i = 0; i < arr.length; i++) {
    let j = i;
    while (j < arr.length) {
      console.log(arr[i], arr[j]);
      j++;
    }
  }
}
```

**Analysis:**
- Outer loop: i goes from 0 to n
- Inner loop: j goes from i to n
- When i=0, inner runs n times
- When i=1, inner runs n-1 times
- When i=2, inner runs n-2 times
- ...
- Total: n + (n-1) + (n-2) + ... + 1 = n(n+1)/2 ≈ n²/2
- Drop the constant (1/2)

**Answer: O(n²)**

---

## Example 9 — Space Complexity

Big O also applies to **space** (memory), not just time.

```javascript
function createArray(n) {
  let arr = [];
  for (let i = 0; i < n; i++) {
    arr.push(i);   // adds one item to memory for each i
  }
  return arr;
}
```

**Time complexity:** O(n) — one loop
**Space complexity:** O(n) — the array grows to size n

```javascript
function addNumbers(arr) {
  let total = 0;          // one variable — O(1) space
  for (let i = 0; i < arr.length; i++) {
    total += arr[i];      // no new memory allocated in loop
  }
  return total;
}
```

**Time complexity:** O(n)
**Space complexity:** O(1) — only one extra variable, regardless of arr size

---

## Common Space Complexities

| Space | Meaning | Example |
|-------|---------|---------|
| O(1) | Fixed memory — doesn't grow | One or two variables |
| O(n) | Memory grows with input | Creating a new array of size n |
| O(n²) | Memory grows as n squared | 2D grid/matrix of size n×n |

---

## Practice Problems

For each code snippet, determine the **time complexity** and **space complexity**.

**Problem 1:**
```javascript
function mystery1(n) {
  let count = 0;
  for (let i = 0; i < n; i++) {
    for (let j = i; j < n; j++) {
      count++;
    }
  }
  return count;
}
```

**Problem 2:**
```javascript
function mystery2(arr) {
  if (arr.length === 0) return 0;
  return arr[0] + mystery2(arr.slice(1));
}
```
*(Hint: arr.slice(1) creates a new array each time)*

**Problem 3:**
```javascript
function mystery3(n) {
  let result = [];
  for (let i = 0; i < n; i++) {
    result.push(i * 2);
  }
  return result;
}
```

---

## Answers

**Problem 1:**
- Nested loops, but inner loop starts at i (not 0)
- Total iterations: n + (n-1) + ... + 1 = n²/2 ≈ n²
- Time: **O(n²)**
- Space: **O(1)** — just the `count` variable

**Problem 2:**
- This is a recursive function (we'll learn recursion in Phase 7)
- It calls itself once per item, n times total
- `arr.slice(1)` creates a new array each time → n arrays in memory
- Time: **O(n)**
- Space: **O(n)** — n arrays created

**Problem 3:**
- One loop → O(n) time
- Creates array of size n → O(n) space
- Time: **O(n)**
- Space: **O(n)**

---

## Summary — The Cheat Sheet

```
One operation                     → O(1)
One loop                          → O(n)
Two separate loops                → O(n) (not O(2n))
Nested loops (both n)             → O(n²)
Nested loops (different sizes)    → O(n × m)
Halving repeatedly                → O(log n)
Loop + halving inside             → O(n log n)
Recursion that branches twice     → O(2ⁿ)
```

---

**Phase 1 — Big O is complete.**

You now understand how to measure and compare the speed of code. This is the foundation. Every time you learn a new data structure or algorithm, you'll ask: "What is its Big O?"

**Next: Day 8 — Arrays**
The most fundamental data structure. You've been using them — now you'll understand what's actually happening.
