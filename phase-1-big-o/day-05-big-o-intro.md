# Day 5 — Big O Notation: What Is It?

---

## The Problem Big O Solves

Imagine two people write code to solve the same problem.
Both solutions give the correct answer.
Which one is better?

That is exactly what **Big O Notation** answers.

Big O is a way to **measure and compare how fast or slow code is**.

More specifically: Big O tells you **how the speed of your code changes as the input gets bigger**.

---

## Why "As the Input Gets Bigger"?

With small inputs, almost any code is fast. The difference shows up when data is large.

Example: searching for a name in a list.

- With 10 names: both slow and fast code finish instantly
- With 1,000,000 names: slow code takes 10 seconds, fast code takes milliseconds

Big O measures what happens in the **worst case**, with **large inputs**.

---

## An Analogy — Driving to the Airport

Imagine you need to deliver files to someone at the airport.

**Option 1: Drive them yourself**
- 10 files → 30 minutes drive
- 1,000 files → 30 minutes drive (you just carry more files in the car)
- 1,000,000 files → 30 minutes drive (same trip, bigger bag)

The time doesn't depend on the number of files. It's always one trip.
This is **O(1)** — constant time.

**Option 2: Walk one file at a time**
- 1 file → 30 minutes
- 10 files → 300 minutes (10 trips)
- 1,000 files → 30,000 minutes (1,000 trips)

The time grows directly with the number of files.
This is **O(n)** — linear time.

**Option 3: For each file, walk it there AND walk back for every other file**
- 1 file → 1 trip
- 10 files → 100 trips (each file causes 10 more trips)
- 1,000 files → 1,000,000 trips

The time grows as files² (files squared).
This is **O(n²)** — quadratic time.

---

## What Does the "n" Mean?

In Big O, **n** means **the size of the input**.

- If you have a list of 100 items, n = 100
- If you have a list of 1,000,000 items, n = 1,000,000
- If you search through a string of 50 characters, n = 50

When we write O(n), we're saying: "the number of steps this code takes is proportional to n".

---

## How to Count Steps in Code

Every line of code that "does work" counts as a step.

```javascript
// Example 1:
function printFirst(arr) {
  console.log(arr[0]);  // 1 step
}
```
No matter how large the array is, this always does exactly 1 step.
This is **O(1)**.

```javascript
// Example 2:
function printAll(arr) {
  for (let i = 0; i < arr.length; i++) {  // runs n times
    console.log(arr[i]);                   // 1 step each time
  }
}
```
If arr has 5 items → 5 steps
If arr has 100 items → 100 steps
If arr has n items → n steps
This is **O(n)**.

```javascript
// Example 3:
function printAllPairs(arr) {
  for (let i = 0; i < arr.length; i++) {      // runs n times
    for (let j = 0; j < arr.length; j++) {    // runs n times FOR EACH i
      console.log(arr[i], arr[j]);
    }
  }
}
```
If arr has 3 items → 3 × 3 = 9 steps
If arr has 10 items → 10 × 10 = 100 steps
If arr has n items → n × n = n² steps
This is **O(n²)**.

---

## The Rules of Big O

### Rule 1: Drop constants

```javascript
function example(arr) {
  console.log(arr[0]);  // 1 step
  console.log(arr[1]);  // 1 step
  console.log(arr[2]);  // 1 step
}
```
This does exactly 3 steps always. You might think it's O(3).
But we write it as **O(1)**.

Why? Because constants don't matter when n gets very large.
O(1) and O(3) and O(100) are all "constant time" — they don't grow with n.

```javascript
function printAllTwice(arr) {
  for (let i = 0; i < arr.length; i++) {
    console.log(arr[i]);
  }
  for (let i = 0; i < arr.length; i++) {
    console.log(arr[i]);
  }
}
```
This loops through the array twice → 2n steps.
You might think O(2n). But we write **O(n)**.
We drop the constant (2) because it doesn't change the overall growth pattern.

### Rule 2: Drop smaller terms

```javascript
function example(arr) {
  console.log(arr[0]);              // O(1)

  for (let i = 0; i < arr.length; i++) {  // O(n)
    console.log(arr[i]);
  }

  for (let i = 0; i < arr.length; i++) {      // O(n²)
    for (let j = 0; j < arr.length; j++) {
      console.log(arr[i], arr[j]);
    }
  }
}
```
Total steps: 1 + n + n²

When n is large (like 1,000,000):
- 1 is tiny, ignore it
- n = 1,000,000
- n² = 1,000,000,000,000 (one trillion!)

The n² completely dominates. So we write **O(n²)** and ignore the smaller parts.

---

## Big O Is About the Worst Case

Usually, when we talk about Big O, we talk about the **worst case** scenario.

Example — searching for a name in a list:
```javascript
function findName(names, target) {
  for (let i = 0; i < names.length; i++) {
    if (names[i] === target) {
      return i;  // found it!
    }
  }
  return -1;  // not found
}
```
- **Best case:** Target is the first item → 1 step → O(1)
- **Average case:** Target is in the middle → n/2 steps → O(n)
- **Worst case:** Target is the last item OR not in the list → n steps → O(n)

We say this function is **O(n)** because in the worst case it looks at all n items.

---

## Summary

| Big O | Name | Meaning |
|-------|------|---------|
| O(1) | Constant | Always same number of steps, no matter the input size |
| O(n) | Linear | Steps grow directly with input size |
| O(n²) | Quadratic | Steps grow as n squared (loop inside a loop) |
| O(log n) | Logarithmic | Steps grow very slowly (cut problem in half each time) |

---

## How to Think When Analyzing Code

Ask yourself:
1. Does this code run the same number of steps regardless of input? → **O(1)**
2. Does this code loop through the input once? → **O(n)**
3. Does this code have a loop inside a loop? → **O(n²)**
4. Does this code keep cutting the input in half? → **O(log n)**

---

**Next: Day 6 — Big O Common Complexities**
We will go through every common Big O in detail with examples and pictures.
