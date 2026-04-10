# Day 9 — Arrays: First Problems

---

## How to Approach Any DSA Problem

Before writing a single line of code, follow these steps every time:

**Step 1: Understand the problem**
Read it twice. What is the input? What should the output be?

**Step 2: Look at examples**
Work through the examples by hand (with pencil and paper, or in your head).

**Step 3: Think of the simplest solution first**
Don't think about speed yet. Just get something that works.

**Step 4: Write the code**
Code the simple solution.

**Step 5: Analyze the Big O**
How fast is it? Can it be improved?

**Step 6: Try to improve (if needed)**
Can you do it faster?

---

Let's solve our first real problems together.

---

## Problem 1: Find the Maximum Number

**Problem:** Given an array of numbers, return the largest number.

**Input:** `[3, 7, 2, 9, 4]`
**Output:** `9`

**Step 1: Understand**
Go through the array and find the biggest number.

**Step 2: Think through it manually**
- Look at 3. It's the biggest so far.
- Look at 7. Is 7 > 3? Yes. New biggest: 7.
- Look at 2. Is 2 > 7? No. Still 7.
- Look at 9. Is 9 > 7? Yes. New biggest: 9.
- Look at 4. Is 4 > 9? No. Still 9.
- Done. Answer: 9.

**Step 3 & 4: Write the code**
```javascript
function findMax(arr) {
  // Start by assuming the first element is the largest
  let max = arr[0];

  // Go through every element
  for (let i = 1; i < arr.length; i++) {  // start at 1, we already have arr[0]
    if (arr[i] > max) {     // is this element bigger than our current max?
      max = arr[i];         // yes — update max
    }
  }

  return max;
}

console.log(findMax([3, 7, 2, 9, 4]));  // 9
console.log(findMax([1, 1, 1, 1]));     // 1
console.log(findMax([-5, -1, -10]));    // -1
```

**Step 5: Big O**
- One loop through all elements → O(n) time
- Only one extra variable (`max`) → O(1) space

---

## Problem 2: Reverse an Array

**Problem:** Given an array, return a new array with the elements in reverse order.

**Input:** `[1, 2, 3, 4, 5]`
**Output:** `[5, 4, 3, 2, 1]`

**Think through it manually:**
```
Original: [1, 2, 3, 4, 5]
           ↑           ↑
        index 0     index 4 (last)

Reversed: [5, 4, 3, 2, 1]
```

**Solution 1: Build new array from the end**
```javascript
function reverseArray(arr) {
  let reversed = [];  // empty array to fill

  // Start from the last element, go backward
  for (let i = arr.length - 1; i >= 0; i--) {
    reversed.push(arr[i]);
  }

  return reversed;
}

console.log(reverseArray([1, 2, 3, 4, 5]));  // [5, 4, 3, 2, 1]
```

- Time: O(n) — one loop
- Space: O(n) — new array of same size

**Solution 2: Swap from both ends (in-place, better space)**

Instead of making a new array, swap the first and last, then the second and second-to-last, and so on:

```
[1, 2, 3, 4, 5]
 ↑           ↑
swap 1 and 5

[5, 2, 3, 4, 1]
    ↑       ↑
  swap 2 and 4

[5, 4, 3, 2, 1]
        ↑
    middle — stop here
```

```javascript
function reverseInPlace(arr) {
  let left = 0;                // start pointer
  let right = arr.length - 1; // end pointer

  while (left < right) {      // keep going until pointers meet in the middle
    // Swap arr[left] and arr[right]
    let temp = arr[left];    // save left value
    arr[left] = arr[right];  // put right value in left spot
    arr[right] = temp;       // put saved left value in right spot

    left++;   // move left pointer right
    right--;  // move right pointer left
  }

  return arr;
}

console.log(reverseInPlace([1, 2, 3, 4, 5]));  // [5, 4, 3, 2, 1]
```

- Time: O(n) — visits each element once (in pairs)
- Space: O(1) — only `temp`, `left`, `right` variables (no new array!)

This is the **Two Pointers** technique — two variables that move from opposite ends. You'll see this pattern a LOT in DSA.

---

## Problem 3: Check If Array Contains a Duplicate

**Problem:** Given an array of numbers, return `true` if any number appears more than once. Return `false` if all numbers are unique.

**Input:** `[1, 2, 3, 1]` → `true` (1 appears twice)
**Input:** `[1, 2, 3, 4]` → `false` (all unique)

**Simple solution (O(n²) — slow):**
Check every pair of elements:
```javascript
function hasDuplicateSlow(arr) {
  for (let i = 0; i < arr.length; i++) {
    for (let j = i + 1; j < arr.length; j++) {  // j starts AFTER i
      if (arr[i] === arr[j]) {
        return true;  // found a duplicate!
      }
    }
  }
  return false;  // no duplicate found
}
```
This works but it's O(n²) — for each element, we compare with every other element.

**Better solution (O(n) — fast) using a Set:**

A `Set` is like an array but it only keeps **unique values** and lets you check membership in O(1).

```javascript
function hasDuplicate(arr) {
  const seen = new Set();  // empty set

  for (const num of arr) {
    if (seen.has(num)) {   // have we seen this number before?
      return true;          // yes — it's a duplicate!
    }
    seen.add(num);          // no — add it to seen
  }

  return false;  // never found a duplicate
}

console.log(hasDuplicate([1, 2, 3, 1]));  // true
console.log(hasDuplicate([1, 2, 3, 4]));  // false
```

Trace through `[1, 2, 3, 1]`:
- num=1: seen={}, 1 not in seen, add 1 → seen={1}
- num=2: 2 not in seen, add 2 → seen={1,2}
- num=3: 3 not in seen, add 3 → seen={1,2,3}
- num=1: 1 IS in seen → return true ✓

- Time: O(n) — one loop
- Space: O(n) — Set grows up to n elements

---

## Problem 4: Rotate Array

**Problem:** Given an array and a number `k`, move the last `k` elements to the front.

**Input:** `arr = [1, 2, 3, 4, 5]`, `k = 2`
**Output:** `[4, 5, 1, 2, 3]`

The last 2 elements (4, 5) moved to the front.

**Understanding it:**
```
Original:  [1, 2, 3, 4, 5]
k = 2, so last 2 = [4, 5]
Rest = [1, 2, 3]
Result = [4, 5] + [1, 2, 3] = [4, 5, 1, 2, 3]
```

**Solution using slice:**
```javascript
function rotate(arr, k) {
  // k could be larger than array length, handle that:
  k = k % arr.length;  // e.g., if arr has 5 items and k=7, same as k=2

  // Take last k elements + take first (length - k) elements
  return [...arr.slice(arr.length - k), ...arr.slice(0, arr.length - k)];
}

console.log(rotate([1, 2, 3, 4, 5], 2));  // [4, 5, 1, 2, 3]
console.log(rotate([1, 2, 3, 4, 5], 1));  // [5, 1, 2, 3, 4]
```

Breaking it down:
```javascript
arr = [1, 2, 3, 4, 5], k = 2

arr.slice(arr.length - k)      // arr.slice(5 - 2) = arr.slice(3) = [4, 5]
arr.slice(0, arr.length - k)   // arr.slice(0, 3) = [1, 2, 3]

[...arr.slice(3), ...arr.slice(0, 3)]
= [...[4, 5], ...[1, 2, 3]]
= [4, 5, 1, 2, 3] ✓
```

- Time: O(n) — slice creates copies
- Space: O(n) — new array

---

## Today's Practice Problems (Solve Yourself First!)

**Practice 1:** Write a function that returns the second largest number in an array.
```
Input: [3, 7, 2, 9, 4]
Output: 7
```

**Practice 2:** Write a function that removes all duplicates from an array and returns only unique values.
```
Input: [1, 2, 2, 3, 3, 3, 4]
Output: [1, 2, 3, 4]
```

**Practice 3:** Write a function that counts how many times a value appears in an array.
```
Input: [1, 2, 2, 3, 2, 1], value = 2
Output: 3
```

---

## Answers

**Practice 1:**
```javascript
function secondLargest(arr) {
  let largest = -Infinity;
  let second = -Infinity;

  for (const num of arr) {
    if (num > largest) {
      second = largest;  // old largest becomes second
      largest = num;     // new largest
    } else if (num > second && num !== largest) {
      second = num;      // update second if bigger and not equal to largest
    }
  }

  return second;
}
```

**Practice 2:**
```javascript
function removeDuplicates(arr) {
  return [...new Set(arr)];
  // Set keeps only unique values
  // Spread (...) converts Set back to array
}
```

**Practice 3:**
```javascript
function countOccurrences(arr, value) {
  let count = 0;
  for (const item of arr) {
    if (item === value) count++;
  }
  return count;
}
// Or one-liner:
// return arr.filter(item => item === value).length;
```

---

**Next: Day 10 — Two Pointers Technique**
One of the most important patterns in array problems. Turns O(n²) into O(n).
