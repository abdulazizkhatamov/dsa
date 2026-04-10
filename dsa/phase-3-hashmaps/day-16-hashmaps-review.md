# Day 16 — Hash Maps: More Problems and Review

---

## Combining Hash Maps With Other Techniques

The real power of Hash Maps shows when you combine them with techniques you already know. Today we'll solve problems that mix maps with arrays, strings, and sliding window.

---

## Problem 1: Group Anagrams

**Problem:** Given an array of words, group words that are anagrams of each other.

**Input:** `["eat", "tea", "tan", "ate", "nat", "bat"]`
**Output:** `[["eat","tea","ate"], ["tan","nat"], ["bat"]]`

**Key insight:** Two words are anagrams if their **sorted version is the same**.
- "eat" sorted → "aet"
- "tea" sorted → "aet" (same!)
- "tan" sorted → "ant"
- "nat" sorted → "ant" (same!)

So we use the sorted word as a **Map key** and group matching words together.

```javascript
function groupAnagrams(words) {
  const groups = new Map();  // sortedWord → [original words]

  for (const word of words) {
    const key = word.split('').sort().join('');  // sort the letters

    if (!groups.has(key)) {
      groups.set(key, []);  // create new group
    }
    groups.get(key).push(word);  // add word to its group
  }

  return [...groups.values()];  // return all groups as array
}

console.log(groupAnagrams(["eat", "tea", "tan", "ate", "nat", "bat"]));
// [["eat","tea","ate"], ["tan","nat"], ["bat"]]
```

**Trace:**
```
word="eat": key="aet", groups={aet: ["eat"]}
word="tea": key="aet", groups={aet: ["eat","tea"]}
word="tan": key="ant", groups={aet: [...], ant: ["tan"]}
word="ate": key="aet", groups={aet: ["eat","tea","ate"], ...}
word="nat": key="ant", groups={..., ant: ["tan","nat"]}
word="bat": key="abt", groups={..., abt: ["bat"]}
```

- Time: O(n × m log m) — n words, each sorted in O(m log m) where m = word length
- Space: O(n × m) — storing all words in groups

---

## Problem 2: Longest Subarray With K Distinct Characters

**Problem:** Find the length of the longest subarray that contains at most `k` distinct values.

**Input:** `arr = [1, 2, 1, 2, 3]`, `k = 2`
**Output:** `4` (subarray `[1, 2, 1, 2]` has 2 distinct values)

**Technique:** Sliding Window + Map (track count of each element in window)

```javascript
function longestWithKDistinct(arr, k) {
  const freq = new Map();  // element → count in current window
  let left = 0;
  let maxLength = 0;

  for (let right = 0; right < arr.length; right++) {
    // Add right element to window
    freq.set(arr[right], (freq.get(arr[right]) || 0) + 1);

    // If too many distinct values, shrink from left
    while (freq.size > k) {
      freq.set(arr[left], freq.get(arr[left]) - 1);
      if (freq.get(arr[left]) === 0) {
        freq.delete(arr[left]);  // remove from map if count reaches 0
      }
      left++;
    }

    maxLength = Math.max(maxLength, right - left + 1);
  }

  return maxLength;
}

console.log(longestWithKDistinct([1, 2, 1, 2, 3], 2));  // 4
console.log(longestWithKDistinct([1, 2, 1, 3, 4], 2));  // 3
console.log(longestWithKDistinct([1, 2, 3], 3));         // 3 (whole array)
```

**Trace through `[1, 2, 1, 2, 3]`, k=2:**
```
right=0: add 1. freq={1:1}. size=1 ≤ 2. length=1, max=1
right=1: add 2. freq={1:1,2:1}. size=2 ≤ 2. length=2, max=2
right=2: add 1. freq={1:2,2:1}. size=2 ≤ 2. length=3, max=3
right=3: add 2. freq={1:2,2:2}. size=2 ≤ 2. length=4, max=4
right=4: add 3. freq={1:2,2:2,3:1}. size=3 > 2!
  remove arr[0]=1: freq={1:1,2:2,3:1}. size still 3 > 2!
  left=1, remove arr[1]=2: freq={1:1,2:1,3:1}. size still 3 > 2!
  left=2, remove arr[2]=1: freq={2:1,3:1}. size=2 ≤ 2. Stop.
  left=3. length=4-3+1=2, max stays 4.

Answer: 4 ✓
```

---

## Problem 3: Contains Nearby Duplicate

**Problem:** Given an array and a number `k`, return `true` if there are two equal values whose indexes are at most `k` positions apart.

**Input:** `arr = [1, 2, 3, 1]`, `k = 3`
**Output:** `true` (arr[0]=1 and arr[3]=1, distance = 3)

**Input:** `arr = [1, 2, 3, 1]`, `k = 2`
**Output:** `false` (the two 1s are 3 positions apart, which is > 2)

**Approach:** Use a Map to store the most recent index of each value.

```javascript
function containsNearbyDuplicate(arr, k) {
  const lastSeen = new Map();  // value → most recent index

  for (let i = 0; i < arr.length; i++) {
    if (lastSeen.has(arr[i])) {
      const prevIndex = lastSeen.get(arr[i]);
      if (i - prevIndex <= k) return true;  // close enough!
    }
    lastSeen.set(arr[i], i);  // update most recent index
  }

  return false;
}

console.log(containsNearbyDuplicate([1, 2, 3, 1], 3));  // true
console.log(containsNearbyDuplicate([1, 2, 3, 1], 2));  // false
console.log(containsNearbyDuplicate([1, 0, 1, 1], 1));  // true (arr[2] and arr[3])
```

---

## Problem 4: Longest Consecutive Sequence

**Problem:** Given an array of integers, find the length of the longest sequence of consecutive numbers.

**Input:** `[100, 4, 200, 1, 3, 2]`
**Output:** `4` (sequence: 1, 2, 3, 4)

**Input:** `[0, 3, 7, 2, 5, 8, 4, 6, 0, 1]`
**Output:** `9` (sequence: 0,1,2,3,4,5,6,7,8)

**Key insight:** Put all numbers in a Set (O(1) lookup). Then for each number, only start counting if it's the **beginning** of a sequence (meaning `n-1` is NOT in the set).

```javascript
function longestConsecutive(nums) {
  const numSet = new Set(nums);  // for O(1) lookup
  let longest = 0;

  for (const num of numSet) {
    // Only start a sequence from the smallest number in that sequence
    if (!numSet.has(num - 1)) {
      let current = num;
      let length = 1;

      // Count upward while next number exists
      while (numSet.has(current + 1)) {
        current++;
        length++;
      }

      longest = Math.max(longest, length);
    }
  }

  return longest;
}

console.log(longestConsecutive([100, 4, 200, 1, 3, 2]));  // 4
console.log(longestConsecutive([0, 3, 7, 2, 5, 8, 4, 6, 0, 1]));  // 9
```

**Trace through `[100, 4, 200, 1, 3, 2]`:**
```
numSet = {100, 4, 200, 1, 3, 2}

num=100: has 99? No → start sequence. Count: 100, 101? No. length=1
num=4:   has 3? Yes → skip (3 starts this sequence, not 4)
num=200: has 199? No → start. Count: 200, 201? No. length=1
num=1:   has 0? No → start. Count: 1,2,3,4. 5? No. length=4 ✓
num=3:   has 2? Yes → skip
num=2:   has 1? Yes → skip

Longest = 4 ✓
```

- Time: O(n) — each number is processed at most twice (once as start, once in a chain)
- Space: O(n) — the Set stores all n numbers

---

## When to Use a Map vs a Set

| You need to... | Use |
|----------------|-----|
| Check if value exists | `Set` |
| Remove duplicates | `Set` |
| Count occurrences | `Map` (value → count) |
| Store index of element | `Map` (value → index) |
| Group elements | `Map` (key → array) |
| Cache computed results | `Map` (input → output) |

---

## Phase 3 Self-Check

Make sure you can answer without looking:

- [ ] What is a Hash Map? What does it store?
- [ ] What is the Big O of `map.get()`, `map.set()`, `map.has()`?
- [ ] What is the difference between a Map and a Set?
- [ ] How do you count character frequencies in a string?
- [ ] How do you check if two strings are anagrams?
- [ ] How do you solve Two Sum using a Map?
- [ ] When would you combine a sliding window with a Map?

---

## Summary: The 3 Phases So Far

```
Phase 0 (Days 1-4):   Math foundations — exponents, log, modulo
Phase 1 (Days 5-7):   Big O — measure speed of code
Phase 2 (Days 8-13):  Arrays — the foundation data structure
Phase 3 (Days 14-16): Hash Maps — instant lookups, counting, grouping
```

---

**Next: Day 17 — Strings: What They Are and Common Patterns**
Strings are arrays of characters — but they have their own patterns and tricks worth studying separately.
