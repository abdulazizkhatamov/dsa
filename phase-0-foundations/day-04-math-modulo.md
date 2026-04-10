# Day 4 — Math: Modulo (Remainder)

---

## What is Modulo?

Modulo is just the **remainder** after division.

You already know this from primary school, you just didn't know it had a fancy name.

Example:
```
10 ÷ 3 = 3  remainder  1
```
- 3 goes into 10 three full times (3 × 3 = 9)
- 10 - 9 = 1 is left over
- That leftover is the **remainder** — and that is what modulo gives you

In programming (and in math), modulo is written with the `%` symbol:
```javascript
10 % 3 = 1     // 10 divided by 3, remainder is 1
```

---

## More Examples

```javascript
10 % 2 = 0    // 10 ÷ 2 = 5 exactly, no remainder
11 % 2 = 1    // 11 ÷ 2 = 5 remainder 1
12 % 5 = 2    // 12 ÷ 5 = 2 remainder 2
15 % 4 = 3    // 15 ÷ 4 = 3 remainder 3
20 % 6 = 2    // 20 ÷ 6 = 3 remainder 2
7  % 7 = 0    // 7 ÷ 7 = 1 exactly, no remainder
3  % 9 = 3    // 3 ÷ 9 = 0 remainder 3 (when dividing by bigger number, result is the number itself)
```

---

## The Most Important Use: Even or Odd

The single most common use of modulo in DSA is checking if a number is **even or odd**.

```
Even numbers are divisible by 2 with no remainder:
4 % 2 = 0   → even
6 % 2 = 0   → even
100 % 2 = 0 → even

Odd numbers have remainder 1 when divided by 2:
3 % 2 = 1   → odd
7 % 2 = 1   → odd
99 % 2 = 1  → odd
```

In JavaScript:
```javascript
function isEven(number) {
  return number % 2 === 0;
}

function isOdd(number) {
  return number % 2 !== 0;
}

console.log(isEven(4));   // true
console.log(isEven(7));   // false
console.log(isOdd(7));    // true
```

---

## Another Important Use: Wrapping Around

Imagine a clock. After 12 comes 1 again — it wraps around.
Modulo creates this same "wrapping" behavior.

```javascript
// Hour hand on a clock
// If current hour is 11 and you add 3 hours:
(11 + 3) % 12 = 2    // not 14, because it wraps around to 2

// Days of the week (0 = Sunday, 1 = Monday ... 6 = Saturday)
// If today is Friday (5) and you add 3 days:
(5 + 3) % 7 = 1      // Monday — wraps around from Saturday past Sunday to Monday
```

You will see this pattern in many DSA problems.

---

## Another Use: Distributing Items

Imagine you have items numbered 0, 1, 2, 3, 4, 5, 6, 7, 8, 9...
and you want to put them in 3 buckets:

```
item 0 → 0 % 3 = 0 → bucket 0
item 1 → 1 % 3 = 1 → bucket 1
item 2 → 2 % 3 = 2 → bucket 2
item 3 → 3 % 3 = 0 → bucket 0  (wraps back)
item 4 → 4 % 3 = 1 → bucket 1
item 5 → 5 % 3 = 2 → bucket 2
item 6 → 6 % 3 = 0 → bucket 0
...
```

This is actually exactly how **Hash Maps** work internally (you'll learn this in Phase 3).

---

## In JavaScript

```javascript
console.log(10 % 3);   // 1
console.log(10 % 2);   // 0
console.log(7  % 3);   // 1
console.log(9  % 3);   // 0

// Checking even/odd
for (let i = 1; i <= 10; i++) {
  if (i % 2 === 0) {
    console.log(i + " is even");
  } else {
    console.log(i + " is odd");
  }
}
// 1 is odd
// 2 is even
// 3 is odd
// 4 is even
// ...
```

---

## Summary

| Symbol | Meaning | Example |
|--------|---------|---------|
| `%` | Remainder after division | `10 % 3 = 1` |
| `n % 2 === 0` | n is even | `4 % 2 === 0` → true |
| `n % 2 !== 0` | n is odd | `7 % 2 !== 0` → true |

---

## Practice

**Question 1:** What is 17 % 5?

**Question 2:** What is 20 % 4?

**Question 3:** Is 42 even or odd? Use modulo to check.

**Question 4:** What is 3 % 10?

---

## Answers

**Question 1:** 17 ÷ 5 = 3 remainder **2**. So 17 % 5 = 2 ✓

**Question 2:** 20 ÷ 4 = 5 remainder **0**. So 20 % 4 = 0 ✓

**Question 3:** 42 % 2 = 0. Even. ✓

**Question 4:** 3 ÷ 10 = 0 remainder **3**. So 3 % 10 = 3 ✓
*(When the number is smaller than the divider, the result is the number itself)*

---

**You now know all the math you need.**

**Next: Day 5 — Big O Notation**
Now we use what we learned to understand how fast or slow code is.
