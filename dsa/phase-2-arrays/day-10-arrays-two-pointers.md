# Day 10 — Two Pointers Technique

---

## What is the Two Pointers Technique?

The Two Pointers technique uses **two variables** (called pointers) to track two positions in an array at the same time. The pointers move toward each other, or in the same direction at different speeds.

It is one of the most important patterns in DSA. It converts many O(n²) problems into O(n).

There are two main styles:

**Style 1: Opposite ends** — start from both ends, move toward middle
**Style 2: Same direction** — both start at left, move right at different speeds (fast/slow)

---

## Style 1: Opposite Ends

One pointer starts at the LEFT (index 0).
One pointer starts at the RIGHT (last index).
They move toward each other.

```
arr = [1, 2, 3, 4, 5]
       ↑           ↑
      left        right
```

### Problem: Is a String a Palindrome?

A palindrome reads the same forward and backward.
- "racecar" → palindrome ✓
- "hello" → not a palindrome ✗
- "abcba" → palindrome ✓

**Without two pointers (harder to think about):**
Reverse the string and compare. O(n) time but O(n) space (new string).

**With two pointers:**
Compare the character at left with the character at right. If they match, move both pointers inward. If they don't match, it's not a palindrome.

```
"racecar"
 r a c e c a r
 ↑           ↑
left=0     right=6

Step 1: r === r? Yes. Move inward.
 r a c e c a r
   ↑       ↑
  left=1  right=5

Step 2: a === a? Yes. Move inward.
 r a c e c a r
     ↑   ↑
    left=2 right=4

Step 3: c === c? Yes. Move inward.
 r a c e c a r
       ↑
     left=3, right=3 (same position)

left is no longer < right — STOP.
All characters matched → palindrome!
```

```javascript
function isPalindrome(str) {
  let left = 0;
  let right = str.length - 1;

  while (left < right) {           // stop when pointers meet
    if (str[left] !== str[right]) {
      return false;                 // mismatch — not a palindrome
    }
    left++;   // move left pointer right
    right--;  // move right pointer left
  }

  return true;  // all characters matched
}

console.log(isPalindrome("racecar"));  // true
console.log(isPalindrome("hello"));    // false
console.log(isPalindrome("abcba"));    // true
console.log(isPalindrome("a"));        // true (single char is palindrome)
```

- Time: O(n) — visits each character once (in pairs)
- Space: O(1) — just two pointer variables

---

### Problem: Two Sum on a Sorted Array

**Problem:** Given a **sorted** array of numbers and a target sum, find two numbers that add up to the target. Return their indexes.

**Input:** `arr = [1, 3, 5, 7, 9]`, `target = 10`
**Output:** `[1, 3]` (indexes of 3 and 7, because 3 + 7 = 10)

**Why two pointers work on sorted arrays:**

Because the array is sorted:
- If `arr[left] + arr[right]` is **too small** → we need a bigger number → move `left` right
- If `arr[left] + arr[right]` is **too big** → we need a smaller number → move `right` left
- If it equals target → found it!

```
arr = [1, 3, 5, 7, 9], target = 10
       ↑           ↑
      left=0    right=4

Step 1: arr[0] + arr[4] = 1 + 9 = 10 = target! Found it.
Answer: [0, 4]

Let's try target = 12:
Step 1: 1 + 9 = 10. Too small. Move left right.
Step 2: 3 + 9 = 12. Exact match! Answer: [1, 4]

Let's try target = 6:
Step 1: 1 + 9 = 10. Too big. Move right left.
Step 2: 1 + 7 = 8. Too big. Move right left.
Step 3: 1 + 5 = 6. Exact match! Answer: [0, 2]
```

```javascript
function twoSumSorted(arr, target) {
  let left = 0;
  let right = arr.length - 1;

  while (left < right) {
    const sum = arr[left] + arr[right];

    if (sum === target) {
      return [left, right];  // found!
    } else if (sum < target) {
      left++;   // sum too small, need bigger left value
    } else {
      right--;  // sum too big, need smaller right value
    }
  }

  return [];  // no solution found
}

console.log(twoSumSorted([1, 3, 5, 7, 9], 10));  // [0, 4]  (1 + 9)
console.log(twoSumSorted([1, 3, 5, 7, 9], 12));  // [1, 4]  (3 + 9)
console.log(twoSumSorted([1, 3, 5, 7, 9], 6));   // [0, 2]  (1 + 5)
```

- Time: O(n) — two pointers scan the array once between them
- Space: O(1)

---

## Style 2: Same Direction (Fast and Slow)

Both pointers start at the left.
One moves FASTER than the other.

This is used when you want to keep some elements and remove others, or when one pointer "reads" and another "writes".

### Problem: Remove Duplicates from Sorted Array

**Problem:** Given a sorted array, remove duplicates IN PLACE (modify the original array). Return the count of unique elements.

**Input:** `[1, 1, 2, 3, 3, 4]`
**Output:** The array should become `[1, 2, 3, 4, ...]` and return `4` (4 unique elements)

**Idea:**
- `slow` pointer: marks where the next unique element should go
- `fast` pointer: reads through the array looking for unique elements

```
[1, 1, 2, 3, 3, 4]
 ↑  ↑
slow fast

Start: slow=0, fast=1

Step 1: arr[fast]=1, arr[slow]=1. Same! Just move fast.
[1, 1, 2, 3, 3, 4]
 ↑     ↑
slow  fast=2

Step 2: arr[fast]=2, arr[slow]=1. Different! 
  Move slow forward, copy 2 there.
[1, 2, 2, 3, 3, 4]
    ↑     ↑
  slow=1  fast=3

Step 3: arr[fast]=3, arr[slow]=2. Different!
  Move slow, copy 3.
[1, 2, 3, 3, 3, 4]
       ↑     ↑
     slow=2  fast=4

Step 4: arr[fast]=3, arr[slow]=3. Same! Just move fast.
[1, 2, 3, 3, 3, 4]
       ↑        ↑
     slow=2   fast=5

Step 5: arr[fast]=4, arr[slow]=3. Different!
  Move slow, copy 4.
[1, 2, 3, 4, 3, 4]
          ↑
        slow=3, fast=6 (out of bounds — done)

Result: slow + 1 = 4 unique elements
Array: [1, 2, 3, 4, ...] (first 4 elements)
```

```javascript
function removeDuplicatesSorted(arr) {
  if (arr.length === 0) return 0;

  let slow = 0;  // position of last unique element

  for (let fast = 1; fast < arr.length; fast++) {
    if (arr[fast] !== arr[slow]) {  // found a new unique element
      slow++;                        // move slow pointer
      arr[slow] = arr[fast];         // place unique element here
    }
    // if same as slow, fast just keeps moving (skip the duplicate)
  }

  return slow + 1;  // count of unique elements
}

let arr = [1, 1, 2, 3, 3, 4];
let count = removeDuplicatesSorted(arr);
console.log(count);        // 4
console.log(arr.slice(0, count));  // [1, 2, 3, 4]
```

- Time: O(n) — one pass through array
- Space: O(1) — in-place, no new array

---

## When to Use Two Pointers

Ask yourself these questions:

1. Is the array sorted? → Two pointers (opposite ends) often works for find-pair problems
2. Do you need to find two elements that satisfy a condition? → Try opposite ends
3. Do you need to filter or modify an array in-place? → Try fast/slow pointers
4. Are you checking for palindromes? → Definitely opposite ends

---

## Summary

| Style | Pointers Start | Movement | Good For |
|-------|---------------|----------|----------|
| Opposite ends | Left=0, Right=last | Move toward each other | Palindrome, sorted two-sum |
| Fast & Slow | Both at left | Different speeds rightward | Remove duplicates, partitioning |

---

## Practice Problems

**Practice 1:** Given a sorted array, return a new array where every element is squared, in sorted order.
```
Input: [-4, -1, 0, 3, 10]
Output: [0, 1, 9, 16, 100]
```
*(Hint: negative numbers squared can be large. Use two pointers from the ends, build result array from biggest to smallest.)*

**Practice 2:** Given a string, return true if it's a palindrome ignoring spaces and uppercase.
```
Input: "A man a plan a canal Panama"
Output: true
```

---

## Answers

**Practice 1:**
```javascript
function sortedSquares(arr) {
  let left = 0;
  let right = arr.length - 1;
  let result = new Array(arr.length);
  let pos = arr.length - 1;  // fill result from the end (largest first)

  while (left <= right) {
    const leftSq = arr[left] * arr[left];
    const rightSq = arr[right] * arr[right];

    if (leftSq > rightSq) {
      result[pos] = leftSq;
      left++;
    } else {
      result[pos] = rightSq;
      right--;
    }
    pos--;
  }

  return result;
}
```

**Practice 2:**
```javascript
function isPalindromeClean(s) {
  // Keep only letters and numbers, lowercase
  const clean = s.toLowerCase().replace(/[^a-z0-9]/g, '');
  let left = 0;
  let right = clean.length - 1;

  while (left < right) {
    if (clean[left] !== clean[right]) return false;
    left++;
    right--;
  }

  return true;
}
```

---

**Next: Day 11 — Sliding Window Technique**
Another essential pattern. Used for subarray and substring problems.
