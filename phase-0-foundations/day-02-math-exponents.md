# Day 2 — Math: Exponents (Powers)

---

## Why You Need This

When we talk about how fast or slow code is, we use words like:
- "This takes 2 steps"
- "This takes n steps"
- "This takes n² steps"
- "This takes 2ⁿ steps"

That last one — **2ⁿ** — is an exponent. You need to understand what it means before we get to Big O (Day 5).

Don't worry. This is very simple math.

---

## What is an Exponent?

An exponent tells you **how many times to multiply a number by itself**.

That's it.

```
2³ means: 2 × 2 × 2 = 8

The 2 is called the BASE
The 3 is called the EXPONENT (or POWER)

You say it out loud as: "2 to the power of 3" or "2 cubed"
```

More examples:
```
2¹ = 2                          (2 multiplied by itself 1 time = just 2)
2² = 2 × 2 = 4                  (2 multiplied by itself 2 times)
2³ = 2 × 2 × 2 = 8              (2 multiplied by itself 3 times)
2⁴ = 2 × 2 × 2 × 2 = 16
2⁵ = 2 × 2 × 2 × 2 × 2 = 32
2¹⁰ = 1024
2²⁰ = 1,048,576  (over 1 million!)
```

See how fast the numbers grow? That is the important thing to notice.

---

## Key Observation — Exponents Grow VERY Fast

Let's compare:
```
n = 10:
  n   = 10         (just 10)
  n²  = 100        (10 × 10)
  2ⁿ  = 1024       (2 × 2 × 2 × 2 × 2 × 2 × 2 × 2 × 2 × 2)

n = 20:
  n   = 20
  n²  = 400
  2ⁿ  = 1,048,576  (over one million!)

n = 30:
  n   = 30
  n²  = 900
  2ⁿ  = 1,073,741,824  (over one BILLION!)
```

This is why in DSA, code that takes **2ⁿ steps** is considered TERRIBLE.
If your code does 2ⁿ steps and someone gives it 30 items, it does a billion operations.

---

## Exponents in JavaScript

```javascript
// In JavaScript, you write exponents with **
console.log(2 ** 3);   // 8
console.log(2 ** 10);  // 1024
console.log(3 ** 3);   // 27  (3 × 3 × 3)
console.log(10 ** 6);  // 1000000
```

---

## Special Cases You Should Know

### Any number to the power of 0 is always 1:
```
2⁰ = 1
5⁰ = 1
100⁰ = 1
```
Why? It's a math rule. You don't need to understand why — just remember it.

### Any number to the power of 1 is itself:
```
2¹ = 2
7¹ = 7
```

### 1 to any power is always 1:
```
1² = 1
1¹⁰⁰ = 1
```

---

## Real World Analogy

Imagine you tell 2 friends a secret.
Then each of those 2 friends tells 2 more people.
Then each of those tells 2 more people.

```
You          → 1 person knows
Round 1      → 2 people know       (2¹)
Round 2      → 4 people know       (2²)
Round 3      → 8 people know       (2³)
Round 4      → 16 people know      (2⁴)
Round 10     → 1024 people know    (2¹⁰)
Round 20     → 1,048,576 people know (2²⁰)
Round 30     → over 1 billion people (2³⁰)
```

This is how viruses spread. This is why 2ⁿ is so dangerous in code.

---

## Practice — Calculate These Yourself

Try to calculate these WITHOUT using a calculator first. Then check.

**Question 1:** What is 2⁶?
**Question 2:** What is 3²?
**Question 3:** What is 10³?
**Question 4:** What is 5⁰?
**Question 5:** What is 2⁸?

---

## Answers

**Question 1:** 2⁶ = 2 × 2 × 2 × 2 × 2 × 2 = 64

**Question 2:** 3² = 3 × 3 = 9

**Question 3:** 10³ = 10 × 10 × 10 = 1000

**Question 4:** 5⁰ = 1 (anything to the power of 0 is 1)

**Question 5:** 2⁸ = 2 × 2 × 2 × 2 × 2 × 2 × 2 × 2 = 256

---

## Summary

| Symbol | Meaning | Example |
|--------|---------|---------|
| 2³ | 2 multiplied by itself 3 times | 2 × 2 × 2 = 8 |
| base | the number being multiplied | 2 in 2³ |
| exponent | how many times to multiply | 3 in 2³ |
| n⁰ | always equals 1 | 7⁰ = 1 |

The most important thing to remember: **exponents grow extremely fast**.

**Next: Day 3 — Logarithms**
Logarithms are the REVERSE of exponents. They are just as important for DSA.
