# Day 26 — Recursion: What It Is and How to Think About It

---

## What is Recursion?

Recursion is when a **function calls itself**.

Instead of using a loop to repeat actions, a recursive function solves a problem by solving a SMALLER version of the same problem.

```javascript
function sayHello(n) {
  if (n === 0) return;   // STOP condition
  console.log("hello");
  sayHello(n - 1);        // call itself with a smaller n
}

sayHello(3);
// prints: hello, hello, hello
```

Every recursive function has two parts:
1. **Base case** — the condition where we STOP (no more recursion)
2. **Recursive case** — the problem gets smaller and we call ourselves

**If you forget the base case, the function calls itself forever — a stack overflow.**

---

## The Call Stack

When a function calls itself, each call is added to the **call stack**. JavaScript remembers where each call needs to return to.

```javascript
function countdown(n) {
  if (n === 0) {
    console.log("Done!");
    return;
  }
  console.log(n);
  countdown(n - 1);
}

countdown(3);
```

What happens internally:
```
countdown(3) called
  → prints 3
  → calls countdown(2)
    → prints 2
    → calls countdown(1)
      → prints 1
      → calls countdown(0)
        → prints "Done!"
        → returns
      ← back to countdown(1), which returns
    ← back to countdown(2), which returns
  ← back to countdown(3), which returns
```

Output: 3, 2, 1, Done!

---

## The Classic Example: Factorial

Factorial of n (written n!) = n × (n-1) × (n-2) × ... × 1

- 5! = 5 × 4 × 3 × 2 × 1 = 120
- 3! = 3 × 2 × 1 = 6
- 1! = 1
- 0! = 1 (by definition)

**Notice the pattern:**
- 5! = 5 × 4!
- 4! = 4 × 3!
- 3! = 3 × 2!

So: `factorial(n) = n × factorial(n - 1)`

```javascript
function factorial(n) {
  // Base case: stop here
  if (n <= 1) return 1;

  // Recursive case: n! = n × (n-1)!
  return n * factorial(n - 1);
}

console.log(factorial(5));  // 120
console.log(factorial(3));  // 6
console.log(factorial(0));  // 1
```

**Call stack trace for factorial(4):**
```
factorial(4)
  = 4 * factorial(3)
          = 3 * factorial(2)
                  = 2 * factorial(1)
                              = 1  ← base case, returns 1
                  = 2 * 1 = 2
          = 3 * 2 = 6
  = 4 * 6 = 24
```

---

## The Classic Example: Fibonacci

The Fibonacci sequence: 0, 1, 1, 2, 3, 5, 8, 13, 21...

Each number is the sum of the two before it:
- `fib(0) = 0`
- `fib(1) = 1`
- `fib(n) = fib(n-1) + fib(n-2)`

```javascript
function fib(n) {
  if (n === 0) return 0;  // base case 1
  if (n === 1) return 1;  // base case 2

  return fib(n - 1) + fib(n - 2);  // recursive case
}

console.log(fib(6));   // 8  (0,1,1,2,3,5,8)
console.log(fib(10));  // 55
```

**The call tree for fib(5):**
```
                fib(5)
              /         \
          fib(4)         fib(3)
         /     \         /    \
      fib(3)  fib(2)  fib(2)  fib(1)
      /   \   /   \   /   \
   fib(2) fib(1) fib(1) fib(0) fib(1) fib(0)
   /   \
fib(1) fib(0)
```

Notice: `fib(3)` is calculated TWICE. This is very wasteful. For `fib(50)`, the same values get recalculated billions of times.

**Big O of naive Fibonacci: O(2ⁿ)** — exponential! This is terrible.

We'll fix this in Phase 12 (Dynamic Programming) with **memoization**.

---

## How to Write a Recursive Function: The 3-Step Formula

1. **Identify the base case** — what is the simplest possible input where you know the answer immediately?

2. **Identify the recursive case** — how can you make the problem SMALLER and call yourself?

3. **Trust the recursion** — assume your function correctly solves the smaller problem, and build on top of that.

---

## Example: Sum of Array Elements

**Problem:** Write a recursive function to sum all elements in an array.

**Step 1 (base case):** If the array is empty, the sum is 0.

**Step 2 (recursive case):** Sum = first element + sum of the rest.

**Step 3 (trust it):** Assume `sum(rest)` correctly returns the sum of the rest.

```javascript
function sumArray(arr) {
  if (arr.length === 0) return 0;  // base case

  return arr[0] + sumArray(arr.slice(1));  // first + sum of rest
}

console.log(sumArray([1, 2, 3, 4, 5]));  // 15
```

**Trace:**
```
sumArray([1,2,3,4,5])
= 1 + sumArray([2,3,4,5])
= 1 + 2 + sumArray([3,4,5])
= 1 + 2 + 3 + sumArray([4,5])
= 1 + 2 + 3 + 4 + sumArray([5])
= 1 + 2 + 3 + 4 + 5 + sumArray([])
= 1 + 2 + 3 + 4 + 5 + 0
= 15 ✓
```

---

## Example: Count Down and Print in Reverse

**Problem:** Print numbers from n down to 1 (then back up) using recursion.

```javascript
function printAroundZero(n) {
  if (n === 0) {
    console.log(0);
    return;
  }

  console.log(n);        // print BEFORE recursive call → going down
  printAroundZero(n - 1);
  console.log(n);        // print AFTER recursive call → going back up
}

printAroundZero(3);
// 3, 2, 1, 0, 1, 2, 3
```

**Key insight:** Code BEFORE the recursive call runs during the "going down" phase. Code AFTER runs during the "coming back up" phase. This matters a lot in tree/graph traversal.

---

## Recursion vs Iteration

Anything you can do with recursion can be done with a loop, and vice versa.

| | Recursion | Iteration |
|--|-----------|-----------|
| Code | Usually shorter, more elegant | Can be more verbose |
| Memory | Uses call stack (can overflow for deep recursion) | Constant memory with a loop |
| Natural for | Trees, graphs, nested structures | Simple linear problems |
| Risk | Stack overflow if too deep | No extra risk |

For most DSA problems with trees and graphs, recursion is the natural choice.

---

## Summary

| Concept | Key Point |
|---------|-----------|
| Base case | When to STOP — required or you get infinite recursion |
| Recursive case | Call yourself with a SMALLER problem |
| Call stack | Each recursive call is added; unwinds as calls return |
| Trust | Assume smaller calls work correctly; just handle the current step |
| Big O | Often O(n) for linear, O(2ⁿ) for branching (before optimization) |

---

## Practice Problems

**Practice 1:** Write a recursive function to calculate 2 to the power of n.
```
Input: 3 → Output: 8  (2³ = 2×2×2 = 8)
Input: 0 → Output: 1  (2⁰ = 1)
```

**Practice 2:** Write a recursive function that reverses an array.
```
Input: [1, 2, 3, 4]
Output: [4, 3, 2, 1]
```

---

## Answers

**Practice 1:**
```javascript
function power(n) {
  if (n === 0) return 1;        // base case: 2⁰ = 1
  return 2 * power(n - 1);     // 2ⁿ = 2 × 2^(n-1)
}

console.log(power(3));  // 8
console.log(power(0));  // 1
console.log(power(5));  // 32
```

**Practice 2:**
```javascript
function reverseArray(arr) {
  if (arr.length <= 1) return arr;  // base case

  // Last element + reverse of everything else
  return [arr[arr.length - 1], ...reverseArray(arr.slice(0, -1))];
}

console.log(reverseArray([1, 2, 3, 4]));  // [4, 3, 2, 1]
```

---

**Next: Day 27 — Recursion: Classic Problems**
Solve the most common recursive interview problems: binary search recursively, string permutations, and more.
