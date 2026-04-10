# Day 15 — Hash Maps: Frequency Counter Pattern

---

## What is the Frequency Counter Pattern?

The Frequency Counter pattern uses a Map (or plain object) to **count how often each value appears**, then uses those counts to solve the problem.

It turns many O(n²) problems — where you'd normally compare every element against every other element — into O(n).

---

## Problem 1: Valid Anagram

**Problem:** Given two strings, return `true` if one is an anagram of the other.

An anagram uses the **exact same letters**, just in a different order.
- `"listen"` and `"silent"` → anagram ✓
- `"hello"` and `"world"` → not an anagram ✗
- `"rat"` and `"tar"` → anagram ✓

**Slow approach (O(n log n)):**
Sort both strings and compare.
```javascript
function isAnagramSlow(s, t) {
  return s.split('').sort().join('') === t.split('').sort().join('');
}
```
Sorting is O(n log n). We can do better.

**Fast approach — Frequency Counter (O(n)):**

Idea:
1. Count how many times each character appears in `s`
2. Count how many times each character appears in `t`
3. If all counts match → anagram

```javascript
function isAnagram(s, t) {
  if (s.length !== t.length) return false;  // quick check: different lengths can't be anagram

  const freqS = new Map();
  const freqT = new Map();

  // Count characters in s
  for (const char of s) {
    freqS.set(char, (freqS.get(char) || 0) + 1);
  }

  // Count characters in t
  for (const char of t) {
    freqT.set(char, (freqT.get(char) || 0) + 1);
  }

  // Compare frequencies
  for (const [char, count] of freqS) {
    if (freqT.get(char) !== count) return false;
  }

  return true;
}

console.log(isAnagram("listen", "silent"));  // true
console.log(isAnagram("hello", "world"));    // false
console.log(isAnagram("rat", "tar"));        // true
console.log(isAnagram("ab", "a"));           // false
```

**Trace through `"rat"` and `"tar"`:**
```
freqS after "rat": {r:1, a:1, t:1}
freqT after "tar": {t:1, a:1, r:1}

Compare freqS vs freqT:
r → freqS=1, freqT=1 ✓
a → freqS=1, freqT=1 ✓
t → freqS=1, freqT=1 ✓

Result: true ✓
```

**Even shorter version using one Map:**
```javascript
function isAnagram(s, t) {
  if (s.length !== t.length) return false;

  const freq = new Map();

  // Increment for s
  for (const char of s) {
    freq.set(char, (freq.get(char) || 0) + 1);
  }

  // Decrement for t
  for (const char of t) {
    if (!freq.has(char)) return false;
    freq.set(char, freq.get(char) - 1);
    if (freq.get(char) < 0) return false;  // t has more of this char than s
  }

  return true;
}
```

---

## Problem 2: Two Sum (Revisited with Map)

You already saw this in Day 13, but let's look at it through the frequency counter lens.

**Problem:** Find two indexes whose values sum to `target`.

**Input:** `arr = [2, 7, 11, 15]`, `target = 9`
**Output:** `[0, 1]`

**The insight:** For each element `x`, we need `target - x`. Use a Map to store values we've already seen.

```javascript
function twoSum(arr, target) {
  const seen = new Map();  // value → index

  for (let i = 0; i < arr.length; i++) {
    const needed = target - arr[i];

    if (seen.has(needed)) {
      return [seen.get(needed), i];
    }

    seen.set(arr[i], i);
  }

  return [];
}

console.log(twoSum([2, 7, 11, 15], 9));   // [0, 1]
console.log(twoSum([3, 2, 4], 6));        // [1, 2]
```

---

## Problem 3: First Non-Repeating Character

**Problem:** Given a string, return the **first character** that appears only once.

**Input:** `"leetcode"`
**Output:** `"l"` (l appears once, first)

**Input:** `"aabb"`
**Output:** `""` (no unique character)

**Approach:**
1. Count frequency of each character
2. Loop through string again — return the first character with count 1

```javascript
function firstUnique(s) {
  const freq = new Map();

  // Step 1: count frequencies
  for (const char of s) {
    freq.set(char, (freq.get(char) || 0) + 1);
  }

  // Step 2: find first with count 1
  for (const char of s) {
    if (freq.get(char) === 1) return char;
  }

  return "";  // no unique character found
}

console.log(firstUnique("leetcode"));  // "l"
console.log(firstUnique("loveleetcode"));  // "v"
console.log(firstUnique("aabb"));  // ""
```

**Trace through `"leetcode"`:**
```
Step 1 — frequencies:
l→1, e→3, t→1, c→1, o→1, d→1

Step 2 — first with count 1:
l → freq=1 → return "l" ✓
```

- Time: O(n) — two passes through the string
- Space: O(1) — at most 26 letters in the map (constant!)

---

## Problem 4: Subarray Equals K (Advanced)

**Problem:** Given an array and a number k, count how many contiguous subarrays have a sum that equals k.

**Input:** `arr = [1, 1, 1]`, `k = 2`
**Output:** `2` (subarrays `[1,1]` at indexes 0-1, and `[1,1]` at indexes 1-2)

This problem combines **Prefix Sum** with a **Map**.

**Key insight:**
- Build prefix sums as we go
- At each position, check: have we seen a prefix sum that is `currentSum - k`?
- If yes, there's a subarray from there to here that sums to k

```
[1, 1, 1], k=2

prefix sums as we go: 0, 1, 2, 3

At sum=2: we need to find if (2-2=0) exists in our seen sums → yes (the initial 0)
At sum=3: we need to find if (3-2=1) exists in our seen sums → yes (after first element)

Count = 2 ✓
```

```javascript
function subarraySum(arr, k) {
  const prefixCounts = new Map();
  prefixCounts.set(0, 1);  // sum of 0 has been "seen" once before we start

  let currentSum = 0;
  let count = 0;

  for (const num of arr) {
    currentSum += num;

    // How many times has (currentSum - k) appeared as a prefix sum?
    if (prefixCounts.has(currentSum - k)) {
      count += prefixCounts.get(currentSum - k);
    }

    // Record this prefix sum
    prefixCounts.set(currentSum, (prefixCounts.get(currentSum) || 0) + 1);
  }

  return count;
}

console.log(subarraySum([1, 1, 1], 2));     // 2
console.log(subarraySum([1, 2, 3], 3));     // 2  ([1,2] and [3])
console.log(subarraySum([1, -1, 1], 1));    // 3
```

- Time: O(n)
- Space: O(n)

This is one of the harder problems — don't worry if it takes a few reads to click.

---

## The Frequency Counter in Interviews

When you see a problem involving:
- **"How many times does X appear?"**
- **"Do two things have the same elements?"**
- **"Find duplicates / unique elements"**
- **"Find pairs that sum to something"**
- **"Group elements by some property"**

→ Think: Frequency Counter with a Map.

The pattern is always:
1. Build a frequency map (one pass through data)
2. Use the map to answer questions (another pass or lookups)

---

## Summary

| Problem Type | Approach |
|-------------|----------|
| Count occurrences | `map.set(key, (map.get(key) || 0) + 1)` |
| Check if two strings are anagrams | Count both, compare |
| Find first unique | Count all, then find first with count 1 |
| Two sum | Store `value → index`, check `target - current` |
| Count subarrays with sum k | Prefix sum + Map |

---

## Practice Problems

**Practice 1:** Given an array of integers, return all elements that appear more than once.
```
Input: [1, 2, 3, 2, 4, 1]
Output: [2, 1]
```

**Practice 2:** Given a string, return the character that appears the most times.
```
Input: "mississippi"
Output: "s"  (appears 4 times)
```

---

## Answers

**Practice 1:**
```javascript
function findDuplicates(arr) {
  const freq = new Map();
  for (const num of arr) {
    freq.set(num, (freq.get(num) || 0) + 1);
  }

  const result = [];
  for (const [num, count] of freq) {
    if (count > 1) result.push(num);
  }

  return result;
}

console.log(findDuplicates([1, 2, 3, 2, 4, 1]));  // [2, 1]
```

**Practice 2:**
```javascript
function mostFrequentChar(str) {
  const freq = new Map();
  for (const char of str) {
    freq.set(char, (freq.get(char) || 0) + 1);
  }

  let maxCount = 0;
  let result = '';
  for (const [char, count] of freq) {
    if (count > maxCount) {
      maxCount = count;
      result = char;
    }
  }

  return result;
}

console.log(mostFrequentChar("mississippi"));  // "s"
```

---

**Next: Day 16 — Hash Maps: More Problems + Review**
We'll solve more problems that mix Maps with other techniques (arrays, strings, sliding window).
