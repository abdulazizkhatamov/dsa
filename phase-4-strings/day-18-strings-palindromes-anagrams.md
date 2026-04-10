# Day 18 — Strings: Palindromes and Anagrams

---

## Why These Two?

Palindromes and anagrams appear constantly in coding interviews. They're good because:
- They seem simple but have interesting edge cases
- They can be solved multiple ways, testing your depth
- They combine techniques: Two Pointers, Hash Maps, sorting

---

## Palindromes

A palindrome reads the same forward and backward.

- `"racecar"` → palindrome ✓
- `"abcba"` → palindrome ✓
- `"hello"` → not a palindrome ✗
- `"a"` → palindrome ✓ (single character is always a palindrome)
- `""` → palindrome ✓ (empty string too, by convention)

### Basic Check (you already know this)

```javascript
function isPalindrome(s) {
  let left = 0;
  let right = s.length - 1;

  while (left < right) {
    if (s[left] !== s[right]) return false;
    left++;
    right--;
  }

  return true;
}
```

---

### Variation 1: Ignore Case and Non-Letter Characters

**Problem:** Check if a string is a palindrome, ignoring spaces, punctuation, and capital letters.

**Input:** `"A man, a plan, a canal: Panama"`
**Output:** `true`

After cleaning: `"amanaplanacanalpanama"` → palindrome ✓

```javascript
function isPalindromeClean(s) {
  // Keep only letters and numbers, convert to lowercase
  const clean = s.toLowerCase().replace(/[^a-z0-9]/g, "");

  let left = 0;
  let right = clean.length - 1;

  while (left < right) {
    if (clean[left] !== clean[right]) return false;
    left++;
    right--;
  }

  return true;
}

console.log(isPalindromeClean("A man, a plan, a canal: Panama"));  // true
console.log(isPalindromeClean("race a car"));  // false
console.log(isPalindromeClean("Was it a car or a cat I saw?"));  // true
```

The regex `/[^a-z0-9]/g`:
- `[^...]` means "NOT these characters"
- `a-z` means any lowercase letter
- `0-9` means any digit
- `g` means replace all occurrences

---

### Variation 2: Valid Palindrome With At Most One Deletion

**Problem:** Given a string, return `true` if you can make it a palindrome by deleting **at most one character**.

**Input:** `"abca"` → `true` (delete 'c': "aba", or delete 'b': "aca")
**Input:** `"abc"` → `false`

**Approach:**
- Use two pointers
- When a mismatch is found, try skipping either the left character or the right character
- If either path leads to a palindrome, return true

```javascript
function isPalindromeRange(s, left, right) {
  while (left < right) {
    if (s[left] !== s[right]) return false;
    left++;
    right--;
  }
  return true;
}

function validPalindromeOneDeletion(s) {
  let left = 0;
  let right = s.length - 1;

  while (left < right) {
    if (s[left] !== s[right]) {
      // Try skipping left character OR skipping right character
      return isPalindromeRange(s, left + 1, right) ||
             isPalindromeRange(s, left, right - 1);
    }
    left++;
    right--;
  }

  return true;  // already a palindrome, no deletion needed
}

console.log(validPalindromeOneDeletion("abca"));  // true
console.log(validPalindromeOneDeletion("abc"));   // false
console.log(validPalindromeOneDeletion("raceacar"));  // true (delete 'a' in middle)
```

---

### Variation 3: Longest Palindromic Substring

**Problem:** Find the longest substring that is a palindrome.

**Input:** `"babad"`
**Output:** `"bab"` (or `"aba"` — both valid)

**Input:** `"cbbd"`
**Output:** `"bb"`

**Technique: Expand Around Center**

A palindrome expands symmetrically from its center. For each position (and each pair of positions for even-length palindromes), try expanding outward.

```javascript
function longestPalindrome(s) {
  let start = 0;
  let maxLength = 1;

  function expandFromCenter(left, right) {
    while (left >= 0 && right < s.length && s[left] === s[right]) {
      const length = right - left + 1;
      if (length > maxLength) {
        maxLength = length;
        start = left;
      }
      left--;
      right++;
    }
  }

  for (let i = 0; i < s.length; i++) {
    expandFromCenter(i, i);      // odd-length: center is one character
    expandFromCenter(i, i + 1);  // even-length: center is between two characters
  }

  return s.slice(start, start + maxLength);
}

console.log(longestPalindrome("babad"));  // "bab" or "aba"
console.log(longestPalindrome("cbbd"));   // "bb"
console.log(longestPalindrome("a"));      // "a"
console.log(longestPalindrome("racecar")); // "racecar"
```

- Time: O(n²) — for each center, expand up to n times
- Space: O(1)

---

## Anagrams

Two strings are anagrams if they use the **same characters** in the **same quantities**, just in a different order.

- `"listen"` and `"silent"` → anagram ✓
- `"rat"` and `"tar"` → anagram ✓
- `"hello"` and `"world"` → not anagram ✗

You already solved `isAnagram` in Day 15. Today we go deeper.

---

### Variation 1: Find All Anagrams in a String

**Problem:** Given a string `s` and a pattern `p`, return the **starting indexes** of all places in `s` where an anagram of `p` starts.

**Input:** `s = "cbaebabacd"`, `p = "abc"`
**Output:** `[0, 6]`

Explanation:
- Index 0: `"cba"` is an anagram of `"abc"` ✓
- Index 6: `"bac"` is an anagram of `"abc"` ✓

**Technique:** Sliding Window of fixed size (size = p.length) + frequency comparison

```javascript
function findAnagrams(s, p) {
  if (p.length > s.length) return [];

  const result = [];
  const pFreq = new Map();
  const windowFreq = new Map();

  // Count frequency of characters in p
  for (const char of p) {
    pFreq.set(char, (pFreq.get(char) || 0) + 1);
  }

  // Helper: compare two maps
  function mapsEqual(m1, m2) {
    if (m1.size !== m2.size) return false;
    for (const [key, val] of m1) {
      if (m2.get(key) !== val) return false;
    }
    return true;
  }

  // Initialize first window
  for (let i = 0; i < p.length; i++) {
    windowFreq.set(s[i], (windowFreq.get(s[i]) || 0) + 1);
  }

  if (mapsEqual(pFreq, windowFreq)) result.push(0);

  // Slide the window
  for (let i = p.length; i < s.length; i++) {
    // Add new character on the right
    const newChar = s[i];
    windowFreq.set(newChar, (windowFreq.get(newChar) || 0) + 1);

    // Remove old character on the left
    const oldChar = s[i - p.length];
    windowFreq.set(oldChar, windowFreq.get(oldChar) - 1);
    if (windowFreq.get(oldChar) === 0) windowFreq.delete(oldChar);

    // Check if current window is an anagram
    if (mapsEqual(pFreq, windowFreq)) result.push(i - p.length + 1);
  }

  return result;
}

console.log(findAnagrams("cbaebabacd", "abc"));  // [0, 6]
console.log(findAnagrams("abab", "ab"));          // [0, 1, 2]
```

- Time: O(n × 26) which simplifies to O(n) since the alphabet is constant size
- Space: O(1) — maps hold at most 26 characters

---

### Variation 2: Minimum Window Substring

**Problem:** Given strings `s` and `t`, find the smallest window in `s` that contains all characters of `t`.

**Input:** `s = "ADOBECODEBANC"`, `t = "ABC"`
**Output:** `"BANC"` (contains A, B, and C — length 4)

This is a harder problem but uses the same pattern: variable sliding window + Map.

```javascript
function minWindow(s, t) {
  if (t.length > s.length) return "";

  const need = new Map();   // characters we need and how many
  for (const char of t) {
    need.set(char, (need.get(char) || 0) + 1);
  }

  let have = 0;                 // how many distinct chars we've fully satisfied
  const required = need.size;   // how many distinct chars we need to satisfy
  const window = new Map();

  let minLength = Infinity;
  let minStart = 0;
  let left = 0;

  for (let right = 0; right < s.length; right++) {
    const char = s[right];
    window.set(char, (window.get(char) || 0) + 1);

    // Check if this char's requirement is now fully met
    if (need.has(char) && window.get(char) === need.get(char)) {
      have++;
    }

    // Try to shrink window while all requirements are met
    while (have === required) {
      const windowLength = right - left + 1;
      if (windowLength < minLength) {
        minLength = windowLength;
        minStart = left;
      }

      // Remove left character
      const leftChar = s[left];
      window.set(leftChar, window.get(leftChar) - 1);
      if (need.has(leftChar) && window.get(leftChar) < need.get(leftChar)) {
        have--;
      }
      left++;
    }
  }

  return minLength === Infinity ? "" : s.slice(minStart, minStart + minLength);
}

console.log(minWindow("ADOBECODEBANC", "ABC"));  // "BANC"
console.log(minWindow("a", "a"));                // "a"
console.log(minWindow("a", "b"));                // ""
```

- Time: O(n) — each character added and removed at most once
- Space: O(|t|) — maps are bounded by size of t

---

## Summary

| Problem | Technique | Time |
|---------|-----------|------|
| Is palindrome? | Two pointers | O(n) |
| Is palindrome ignoring spaces? | Clean string, two pointers | O(n) |
| Palindrome with one deletion? | Two pointers + helper | O(n) |
| Longest palindromic substring | Expand from center | O(n²) |
| Is anagram? | Frequency map | O(n) |
| Find all anagrams in string | Fixed sliding window + map | O(n) |
| Minimum window substring | Variable sliding window + map | O(n) |

---

## Practice Problems

**Practice 1:** Given a string, find the count of palindromic substrings.
```
Input: "abc"
Output: 3  (each character is its own palindrome: "a","b","c")

Input: "aaa"
Output: 6  ("a","a","a","aa","aa","aaa")
```

**Practice 2:** Check if any permutation (rearrangement) of string `s` is a substring of string `t`.
```
Input: s = "ab", t = "eidbaooo"
Output: true  ("ba" is a substring of t at index 3)
```

---

## Answers

**Practice 1:**
```javascript
function countPalindromes(s) {
  let count = 0;

  function expandFromCenter(left, right) {
    while (left >= 0 && right < s.length && s[left] === s[right]) {
      count++;
      left--;
      right++;
    }
  }

  for (let i = 0; i < s.length; i++) {
    expandFromCenter(i, i);      // odd length
    expandFromCenter(i, i + 1);  // even length
  }

  return count;
}

console.log(countPalindromes("abc"));  // 3
console.log(countPalindromes("aaa"));  // 6
```

**Practice 2:**
```javascript
function checkInclusion(s, t) {
  if (s.length > t.length) return false;

  const sFreq = new Map();
  const windowFreq = new Map();

  for (const char of s) {
    sFreq.set(char, (sFreq.get(char) || 0) + 1);
  }

  for (let i = 0; i < s.length; i++) {
    windowFreq.set(t[i], (windowFreq.get(t[i]) || 0) + 1);
  }

  function mapsEqual(m1, m2) {
    if (m1.size !== m2.size) return false;
    for (const [key, val] of m1) {
      if (m2.get(key) !== val) return false;
    }
    return true;
  }

  if (mapsEqual(sFreq, windowFreq)) return true;

  for (let i = s.length; i < t.length; i++) {
    windowFreq.set(t[i], (windowFreq.get(t[i]) || 0) + 1);
    const old = t[i - s.length];
    windowFreq.set(old, windowFreq.get(old) - 1);
    if (windowFreq.get(old) === 0) windowFreq.delete(old);
    if (mapsEqual(sFreq, windowFreq)) return true;
  }

  return false;
}

console.log(checkInclusion("ab", "eidbaooo"));  // true
console.log(checkInclusion("ab", "eidboaoo"));  // false
```

---

**Next: Day 19 — Strings: Substring Problems and Review**
We'll tackle the remaining key string patterns and review the full string phase.
