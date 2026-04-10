# Day 3 — Math: Logarithms

---

## The Most Important Thing First

Logarithms sound scary. They are not.

A logarithm is just the answer to one question:

> **"2 to the power of WHAT equals this number?"**

That's it. If you understand that sentence, you understand logarithms.

---

## The Question Logarithms Answer

Remember exponents:
```
2³ = 8
(2 to the power of 3 equals 8)
```

A logarithm asks the reverse question:
```
log₂(8) = 3
"2 to the power of WHAT equals 8?"
Answer: 3
```

You read `log₂(8)` as: **"log base 2 of 8"**

More examples:
```
2¹ = 2    →   log₂(2)  = 1    "2 to the power of WHAT equals 2? Answer: 1"
2² = 4    →   log₂(4)  = 2    "2 to the power of WHAT equals 4? Answer: 2"
2³ = 8    →   log₂(8)  = 3    "2 to the power of WHAT equals 8? Answer: 3"
2⁴ = 16   →   log₂(16) = 4    "2 to the power of WHAT equals 16? Answer: 4"
2¹⁰= 1024 →   log₂(1024) = 10  "2 to the power of WHAT equals 1024? Answer: 10"
```

See the pattern? Exponents and logarithms are exact opposites of each other.

---

## Why Logarithms Matter in DSA

Remember from Day 2: exponents grow VERY fast.

Logarithms are the opposite — they grow VERY slowly.

Look at this comparison:
```
Number (n) | log₂(n)
-----------|--------
1          | 0
2          | 1
4          | 2
8          | 3
16         | 4
32         | 5
64         | 6
128        | 7
256        | 8
512        | 9
1024       | 10
1,000,000  | ~20
1,000,000,000 | ~30
```

Even with **1 billion items**, `log₂(1,000,000,000)` is only about **30**.

This is why in DSA, when we say code runs in **O(log n)** time, it means:

> "Even if you give this code 1 billion items, it only does about 30 steps."

That is incredibly fast. This is the holy grail of efficient code.

---

## Real World Analogy — The Phone Book

Imagine you have a phone book with 1,000,000 names sorted A to Z.

You want to find "Smith, John".

**Bad way (no logarithm knowledge):**
Start from page 1, look at every name. Could take 1,000,000 steps.

**Smart way (binary search — uses logarithms):**
1. Open the book to the MIDDLE (page 500,000)
2. You see names starting with "M". Smith comes after M.
3. Ignore the first half. Open to the MIDDLE of the second half.
4. You see names starting with "R". Still before S. Go to the right half.
5. Continue cutting in half each time.

How many times do you cut 1,000,000 in half before you find the name?
```
1,000,000 → 500,000 → 250,000 → 125,000 → 62,500 → 31,250 → 15,625 
→ 7,812 → 3,906 → 1,953 → 976 → 488 → 244 → 122 → 61 
→ 30 → 15 → 7 → 3 → 1
```
That's about **20 steps** to find one name in 1,000,000.

`log₂(1,000,000) ≈ 20`

This is exactly what logarithms describe — **how many times you can cut something in half**.

---

## You Don't Need to Calculate Logarithms

In DSA interviews and study, you never need to calculate exact logarithms.

You just need to know:
1. `log(n)` grows very slowly — even for huge numbers it stays small
2. When you see O(log n) in code, it means the code "cuts the problem in half each step"
3. O(log n) is very good and very fast

The most common logarithm in DSA is **log base 2** (written log₂ or just log).
When people in programming say "log n" they almost always mean log base 2.

---

## In JavaScript

```javascript
// JavaScript has Math.log2() for log base 2
console.log(Math.log2(8));      // 3
console.log(Math.log2(16));     // 4
console.log(Math.log2(1024));   // 10
console.log(Math.log2(1000000)); // ~19.93
```

You will rarely need this in your code — it's more of a thinking tool.

---

## The Big Picture — Speed Comparison

From BEST (fastest) to WORST (slowest):

```
O(1)       → Constant — same speed no matter what. Best possible.
O(log n)   → Logarithmic — grows very slowly. Excellent.
O(n)       → Linear — grows with the input. Acceptable.
O(n log n) → Grows a bit faster than linear. Okay.
O(n²)      → Quadratic — grows very fast. Avoid when possible.
O(2ⁿ)      → Exponential — grows insanely fast. Almost never acceptable.
```

We will explore each of these in Phase 1 (Big O). But now you understand the math behind them.

---

## Summary

| Concept | Meaning | Example |
|---------|---------|---------|
| log₂(n) | "2 to the power of WHAT equals n?" | log₂(8) = 3 because 2³ = 8 |
| Logarithm | The reverse of an exponent | Exponent grows fast, log grows slow |
| O(log n) | Code that halves the problem each step | Very fast — even for huge inputs |

---

## Practice

**Question 1:** log₂(32) = ?  
*(2 to the power of WHAT equals 32?)*

**Question 2:** log₂(64) = ?

**Question 3:** If a program runs in O(log n) time and you give it 1024 items, how many steps does it take approximately?

---

## Answers

**Question 1:** 5. Because 2⁵ = 2 × 2 × 2 × 2 × 2 = 32 ✓

**Question 2:** 6. Because 2⁶ = 64 ✓

**Question 3:** log₂(1024) = 10. Approximately 10 steps. ✓

---

**Next: Day 4 — Modulo (Remainder)**
The last small math topic before we get into Big O.
