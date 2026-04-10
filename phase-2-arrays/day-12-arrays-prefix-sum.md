# Day 12 — Prefix Sum

---

## The Problem With Repeated Sum Calculations

Imagine you have an array of numbers and someone keeps asking you:
- "What is the sum of elements from index 2 to index 5?"
- "What is the sum of elements from index 0 to index 3?"
- "What is the sum of elements from index 1 to index 7?"

Without any preparation, each question requires you to loop through the range and add up the elements — that's O(n) per question.

If someone asks you 1000 questions, that's 1000 × O(n) = very slow.

**Prefix Sum is a technique that lets you answer each of these questions in O(1) — instantly.**

---

## The Idea

Before answering any questions, you do **one preparation step**: build a "prefix sum array".

A prefix sum array stores the **running total** from the beginning up to each index.

```
Original array:
index:  0   1   2   3   4
arr:   [3,  1,  4,  1,  5]

Prefix sum array:
index:  0   1   2   3   4
pre:   [3,  4,  8,  9, 14]

pre[0] = 3             (just arr[0])
pre[1] = 3 + 1 = 4     (arr[0] + arr[1])
pre[2] = 3 + 1 + 4 = 8 (arr[0] + arr[1] + arr[2])
pre[3] = 8 + 1 = 9
pre[4] = 9 + 5 = 14
```

Each `pre[i]` holds the sum of everything from the start up to (and including) index i.

---

## How to Query Any Range

Now, if someone asks: "What is the sum from index 2 to index 4?"

Without prefix sum: loop from 2 to 4, add 4 + 1 + 5 = 10.

With prefix sum:
```
sum(2, 4) = pre[4] - pre[1]
          = 14 - 4
          = 10 ✓
```

**The formula:**
```
sum(left, right) = pre[right] - pre[left - 1]
```

Special case: if `left === 0`:
```
sum(0, right) = pre[right]
```
(Because there's nothing to the left to subtract.)

---

## Why Does The Formula Work?

Think of it this way:

```
pre[right] = sum of everything from index 0 to right
pre[left-1] = sum of everything from index 0 to (left-1)

Difference = only the part from left to right
```

Visual:
```
arr:  [3,  1,  4,  1,  5]
       ←  pre[1] →
       ←       pre[4]       →
               ←  target  →
                (left=2, right=4)

pre[4] - pre[1] = 14 - 4 = 10 ✓
```

---

## Building the Prefix Sum Array

```javascript
function buildPrefixSum(arr) {
  const pre = new Array(arr.length);  // same size as original
  pre[0] = arr[0];                    // first element stays the same

  for (let i = 1; i < arr.length; i++) {
    pre[i] = pre[i - 1] + arr[i];    // add current element to previous running total
  }

  return pre;
}

const arr = [3, 1, 4, 1, 5];
const pre = buildPrefixSum(arr);
console.log(pre);  // [3, 4, 8, 9, 14]
```

---

## Querying a Range

```javascript
function rangeSum(pre, left, right) {
  if (left === 0) {
    return pre[right];
  }
  return pre[right] - pre[left - 1];
}

const arr = [3, 1, 4, 1, 5];
const pre = buildPrefixSum(arr);

console.log(rangeSum(pre, 2, 4));  // 10  (4+1+5)
console.log(rangeSum(pre, 0, 3));  // 9   (3+1+4+1)
console.log(rangeSum(pre, 1, 3));  // 6   (1+4+1)
console.log(rangeSum(pre, 0, 0));  // 3   (just arr[0])
```

---

## Full Example: Answering Multiple Queries

```javascript
function buildPrefixSum(arr) {
  const pre = new Array(arr.length);
  pre[0] = arr[0];
  for (let i = 1; i < arr.length; i++) {
    pre[i] = pre[i - 1] + arr[i];
  }
  return pre;
}

function rangeSum(pre, left, right) {
  if (left === 0) return pre[right];
  return pre[right] - pre[left - 1];
}

const arr = [2, 4, 6, 8, 10, 12];
const pre = buildPrefixSum(arr);

// Now answer 1000 questions in O(1) each:
console.log(rangeSum(pre, 0, 5));  // 42  (sum of whole array)
console.log(rangeSum(pre, 2, 4));  // 24  (6+8+10)
console.log(rangeSum(pre, 1, 3));  // 18  (4+6+8)
console.log(rangeSum(pre, 3, 3));  // 8   (just arr[3])
```

- **Build:** O(n) time, O(n) space (one pass to build pre array)
- **Each query:** O(1) time (just one subtraction)

---

## Real-World Use Case: Number of Students in Each Classroom

Imagine a school hallway with classrooms. You have an array where each value is the number of students in that classroom.

```
classrooms: [10, 15, 8, 20, 12]
```

Question: "How many total students are in classrooms 1 through 3?"

```javascript
const classrooms = [10, 15, 8, 20, 12];
const pre = buildPrefixSum(classrooms);
// pre = [10, 25, 33, 53, 65]

console.log(rangeSum(pre, 1, 3));  // 43  (15+8+20)
```

Without prefix sum, you'd loop from index 1 to 3.
With prefix sum, it's one subtraction: `pre[3] - pre[0]` = 53 - 10 = 43.

---

## Problem: Subarray Sum Equals Target

**Problem:** Given an array, find if there is a contiguous subarray that sums to exactly `target`.

**Input:** `arr = [1, 4, 2, 3, 5]`, `target = 9`
**Output:** `true` (subarray `[4, 2, 3]` has sum 9)

**Approach: Build prefix sums, then check all pairs:**

```javascript
function subarraySumExists(arr, target) {
  const pre = buildPrefixSum(arr);

  // Check every possible subarray [left, right]
  for (let right = 0; right < arr.length; right++) {
    for (let left = 0; left <= right; left++) {
      const sum = left === 0 ? pre[right] : pre[right] - pre[left - 1];
      if (sum === target) return true;
    }
  }

  return false;
}

console.log(subarraySumExists([1, 4, 2, 3, 5], 9));   // true
console.log(subarraySumExists([1, 4, 2, 3, 5], 20));  // false
```

This is O(n²) for the queries — but the query itself is O(1) thanks to prefix sums. Much better than re-summing from scratch each time.

---

## Tracing Through: Running Sum Check

Let's trace `arr = [3, 1, 4, 1, 5]` and verify the prefix sum step by step:

```
i=0: pre[0] = arr[0] = 3
i=1: pre[1] = pre[0] + arr[1] = 3 + 1 = 4
i=2: pre[2] = pre[1] + arr[2] = 4 + 4 = 8
i=3: pre[3] = pre[2] + arr[3] = 8 + 1 = 9
i=4: pre[4] = pre[3] + arr[4] = 9 + 5 = 14

pre = [3, 4, 8, 9, 14] ✓
```

Range query: sum from index 1 to 3?
```
pre[3] - pre[0] = 9 - 3 = 6
Manual check: arr[1]+arr[2]+arr[3] = 1+4+1 = 6 ✓
```

---

## When to Use Prefix Sum

Ask yourself:
1. Do I need the **sum of a subarray** (a range of elements)?
2. Will I be **asked the same type of question multiple times**?
3. Do I need to find a subarray with a specific sum?

If yes → think Prefix Sum.

---

## Summary

| Step | What You Do | Cost |
|------|-------------|------|
| Build prefix array | One pass through original array | O(n) time, O(n) space |
| Answer any range query | One subtraction: `pre[right] - pre[left-1]` | O(1) time |

**The key insight:** Pre-calculate cumulative sums once upfront, then any range sum becomes a simple subtraction instead of a loop.

---

## Practice Problems

**Practice 1:** Given `arr = [1, 2, 3, 4, 5]`, build the prefix sum array and answer:
- Sum from index 0 to 4
- Sum from index 1 to 3
- Sum from index 2 to 2

**Practice 2:** Write a function that returns the average of elements from index `left` to `right` using prefix sums.
```
Input: arr = [4, 2, 8, 6, 10], left = 1, right = 3
Output: 5.33...  (average of 2+8+6=16, divided by 3)
```

---

## Answers

**Practice 1:**
```javascript
const arr = [1, 2, 3, 4, 5];
const pre = buildPrefixSum(arr);
// pre = [1, 3, 6, 10, 15]

console.log(rangeSum(pre, 0, 4));  // 15  (whole array)
console.log(rangeSum(pre, 1, 3));  // 9   (2+3+4)
console.log(rangeSum(pre, 2, 2));  // 3   (just arr[2])
```

**Practice 2:**
```javascript
function rangeAverage(pre, left, right) {
  const sum = left === 0 ? pre[right] : pre[right] - pre[left - 1];
  const count = right - left + 1;
  return sum / count;
}

const arr = [4, 2, 8, 6, 10];
const pre = buildPrefixSum(arr);
console.log(rangeAverage(pre, 1, 3));  // 5.333...  (16/3)
```

---

**Next: Day 13 — Arrays: Review and Mixed Practice**
Before moving on to Hash Maps, we'll combine all array techniques (two pointers + sliding window + prefix sum) and solve harder problems that mix multiple patterns.
