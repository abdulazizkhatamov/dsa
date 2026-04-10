# Day 11 — Sliding Window Technique

---

## What is a Sliding Window?

A sliding window is a technique where you maintain a **range** of elements (called a "window") and **slide** it through the array.

Instead of recalculating everything from scratch each time, you update your result by:
- Adding the new element entering the window (right side)
- Removing the old element leaving the window (left side)

This turns many O(n²) or O(n³) problems into O(n).

---

## The Analogy

Imagine you're looking out of a train window at the scenery.

Your window is fixed in size. As the train moves:
- New scenery enters from the right
- Old scenery disappears on the left

You don't need to look at the whole landscape every second — you just track what enters and exits your window.

---

## Type 1: Fixed-Size Window

The window size stays the same throughout.

### Problem: Maximum Sum of K Consecutive Elements

**Problem:** Given an array and a number k, find the maximum sum of any k consecutive elements.

**Input:** `arr = [2, 1, 5, 1, 3, 2]`, `k = 3`
**Output:** `9` (the subarray `[5, 1, 3]` has sum 9)

**Slow way (O(n × k)):**
```javascript
function maxSumSlow(arr, k) {
  let maxSum = 0;

  for (let i = 0; i <= arr.length - k; i++) {
    let windowSum = 0;
    for (let j = i; j < i + k; j++) {   // inner loop: calculate each window from scratch
      windowSum += arr[j];
    }
    maxSum = Math.max(maxSum, windowSum);
  }

  return maxSum;
}
```
For each starting position, we sum k elements. That's O(n × k).

**Fast way — Sliding Window (O(n)):**

Key insight: when the window slides one step, we don't recalculate the whole sum.
We just:
- ADD the new element entering on the right
- SUBTRACT the element leaving on the left

```
arr = [2, 1, 5, 1, 3, 2], k = 3

Window 1: [2, 1, 5]      → sum = 8
Window 2: [1, 5, 1]      → sum = 8 - 2 + 1 = 7  (removed 2, added 1)
Window 3: [5, 1, 3]      → sum = 7 - 1 + 3 = 9  (removed 1, added 3)
Window 4: [1, 3, 2]      → sum = 9 - 5 + 2 = 6  (removed 5, added 2)

Maximum = 9 ✓
```

```javascript
function maxSum(arr, k) {
  if (arr.length < k) return null;

  // Calculate sum of first window
  let windowSum = 0;
  for (let i = 0; i < k; i++) {
    windowSum += arr[i];
  }

  let maxSum = windowSum;

  // Slide the window
  for (let i = k; i < arr.length; i++) {
    windowSum += arr[i];         // add new element on the right
    windowSum -= arr[i - k];    // remove old element on the left
    maxSum = Math.max(maxSum, windowSum);
  }

  return maxSum;
}

console.log(maxSum([2, 1, 5, 1, 3, 2], 3));  // 9
console.log(maxSum([1, 4, 2, 10, 2, 3], 4)); // 19  (4+2+10+2 or 2+10+2+3)
```

- Time: O(n) — one pass
- Space: O(1) — just a few variables

---

## Type 2: Variable-Size Window

The window size changes based on a condition. The window grows when the condition is met and shrinks when it's violated.

**Template:**
```javascript
function variableWindow(arr, condition) {
  let left = 0;
  let windowState = /* track what's in the window */;
  let result = 0;

  for (let right = 0; right < arr.length; right++) {
    // Add arr[right] to the window
    addToWindow(arr[right]);

    // Shrink window from left if condition is violated
    while (conditionViolated()) {
      removeFromWindow(arr[left]);
      left++;
    }

    // Update result — window is now valid
    result = Math.max(result, right - left + 1);
  }

  return result;
}
```

### Problem: Longest Subarray With Sum ≤ Target

**Problem:** Find the length of the longest subarray where the sum of elements is less than or equal to `target`.

**Input:** `arr = [1, 2, 1, 0, 3]`, `target = 4`
**Output:** `4` (the subarray `[2, 1, 0]` wait, let me trace... `[1,2,1,0]` has sum 4 ≤ 4, length 4)

```javascript
function longestSubarrayWithSum(arr, target) {
  let left = 0;
  let windowSum = 0;
  let maxLength = 0;

  for (let right = 0; right < arr.length; right++) {
    windowSum += arr[right];  // expand window by adding right element

    // If sum exceeds target, shrink from left until it's valid again
    while (windowSum > target) {
      windowSum -= arr[left];
      left++;
    }

    // Now windowSum <= target — this is a valid window
    const currentLength = right - left + 1;
    maxLength = Math.max(maxLength, currentLength);
  }

  return maxLength;
}

console.log(longestSubarrayWithSum([1, 2, 1, 0, 3], 4));  // 4
```

**Tracing through:**
```
arr = [1, 2, 1, 0, 3], target = 4

right=0: add 1, sum=1, left=0, length=1, max=1
right=1: add 2, sum=3, left=0, length=2, max=2
right=2: add 1, sum=4, left=0, length=3, max=3
right=3: add 0, sum=4, left=0, length=4, max=4
right=4: add 3, sum=7 > 4!
  shrink: remove arr[0]=1, sum=6, left=1
  shrink: remove arr[1]=2, sum=4, left=2
  sum=4 ≤ 4, stop shrinking
  length = 4-2+1 = 3, max stays 4

Final answer: 4 ✓
```

---

### Problem: Longest Substring Without Repeating Characters

This is a very famous problem. The window tracks a substring with no repeated characters.

**Input:** `"abcabcbb"`
**Output:** `3` (the substring `"abc"`)

**Input:** `"abba"`
**Output:** `2` (the substring `"ab"` or `"bb"` — wait, `"bb"` has repeat. `"ab"` = 2, `"ba"` = 2)

**Idea:**
- Keep a window of characters with no repeats
- When a repeat is found, shrink from the left until the repeat is removed

```javascript
function longestUniqueSubstring(s) {
  let left = 0;
  let maxLength = 0;
  const seen = new Set();  // characters currently in our window

  for (let right = 0; right < s.length; right++) {
    const char = s[right];

    // If char already in window, remove from left until it's gone
    while (seen.has(char)) {
      seen.delete(s[left]);  // remove leftmost character
      left++;
    }

    seen.add(char);  // add new character to window
    maxLength = Math.max(maxLength, right - left + 1);
  }

  return maxLength;
}

console.log(longestUniqueSubstring("abcabcbb"));  // 3
console.log(longestUniqueSubstring("bbbbb"));     // 1
console.log(longestUniqueSubstring("pwwkew"));    // 3 ("wke")
```

**Tracing through `"abcabc"`:**
```
left=0, seen={}

right=0, char='a': not in seen, add. seen={a}, window="a", len=1
right=1, char='b': not in seen, add. seen={a,b}, window="ab", len=2
right=2, char='c': not in seen, add. seen={a,b,c}, window="abc", len=3
right=3, char='a': IN seen!
  remove s[left=0]='a', left=1. seen={b,c}
  'a' no longer in seen. Add 'a'. seen={b,c,a}, window="bca", len=3
right=4, char='b': IN seen!
  remove s[left=1]='b', left=2. seen={c,a}
  'b' not in seen. Add. seen={c,a,b}, window="cab", len=3
right=5, char='c': IN seen!
  remove s[left=2]='c', left=3. seen={a,b}
  'c' not in seen. Add. seen={a,b,c}, window="abc", len=3

Max = 3 ✓
```

---

## When to Use Sliding Window

Ask yourself:
1. Do I need to find a **subarray** or **substring** with some property?
2. Do I need the **longest** or **shortest** subarray meeting some condition?
3. Do I need a **sum**, **average**, or **count** of elements in a subarray?

If yes → think Sliding Window.

---

## Summary

| Type | Window Size | Used For |
|------|------------|---------|
| Fixed window | Stays same | "sum of every k elements", "average of k elements" |
| Variable window | Grows and shrinks | "longest subarray with property X" |

**The key insight:** By reusing previous calculation (just adding/removing edge elements), we avoid recalculating the whole window each step.

---

## Practice Problems

**Practice 1:** Find the minimum sum subarray of length k.
```
Input: arr = [3, 1, 4, 1, 5, 9, 2, 6], k = 3
Output: 6  (subarray [1, 4, 1] — wait, 1+4+1=6. Or [2,6] is only 2 elements. Let me check: [1,1,4]? No. [3,1] no that's k=2. Actually min of: [3,1,4]=8, [1,4,1]=6, [4,1,5]=10, [1,5,9]=15, [5,9,2]=16, [9,2,6]=17. Answer: 6)
```

**Practice 2:** Find the average of every subarray of size k.
```
Input: arr = [1, 3, 2, 6, -1, 4, 1, 8, 2], k = 5
Output: [2.2, 2.8, 2.4, 3.6, 2.8]
```

---

## Answers

**Practice 1:**
```javascript
function minSum(arr, k) {
  let windowSum = 0;
  for (let i = 0; i < k; i++) windowSum += arr[i];

  let minSum = windowSum;
  for (let i = k; i < arr.length; i++) {
    windowSum += arr[i] - arr[i - k];
    minSum = Math.min(minSum, windowSum);
  }
  return minSum;
}
```

**Practice 2:**
```javascript
function avgSubarrays(arr, k) {
  const result = [];
  let windowSum = 0;
  for (let i = 0; i < k; i++) windowSum += arr[i];
  result.push(windowSum / k);

  for (let i = k; i < arr.length; i++) {
    windowSum += arr[i] - arr[i - k];
    result.push(windowSum / k);
  }
  return result;
}
```

---

**Next: Day 12 — Prefix Sum**
A technique that lets you calculate the sum of any subarray in O(1) time.
