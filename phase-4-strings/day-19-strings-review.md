# Day 19 — Strings: Substring Problems and Review

---

## What is a Substring?

A substring is a contiguous part of a string.

```
s = "abcdef"

"abc"  → substring (from 0 to 2)
"cde"  → substring (from 2 to 4)
"ace"  → NOT a substring (not contiguous — it's a subsequence)
```

**Subsequence** = characters in order, but not necessarily contiguous.
**Substring** = characters in order AND contiguous.

---

## Problem 1: Longest Common Prefix

**Problem:** Given an array of strings, find the longest prefix common to all strings.

**Input:** `["flower", "flow", "flight"]`
**Output:** `"fl"`

**Input:** `["dog", "racecar", "car"]`
**Output:** `""` (no common prefix)

**Approach:** Start with the first word as the prefix. Keep shortening it until all words start with it.

```javascript
function longestCommonPrefix(words) {
  if (words.length === 0) return "";

  let prefix = words[0];  // start with full first word

  for (let i = 1; i < words.length; i++) {
    // Keep shortening prefix until current word starts with it
    while (!words[i].startsWith(prefix)) {
      prefix = prefix.slice(0, prefix.length - 1);  // remove last character
      if (prefix === "") return "";
    }
  }

  return prefix;
}

console.log(longestCommonPrefix(["flower", "flow", "flight"]));  // "fl"
console.log(longestCommonPrefix(["dog", "racecar", "car"]));     // ""
console.log(longestCommonPrefix(["interview", "interval", "inter"]));  // "inter"
```

**Trace through `["flower", "flow", "flight"]`:**
```
prefix = "flower"

i=1, word="flow":
  "flow".startsWith("flower")? No → prefix = "flowe"
  "flow".startsWith("flowe")? No → prefix = "flow"
  "flow".startsWith("flow")? Yes → stop

i=2, word="flight":
  "flight".startsWith("flow")? No → prefix = "flo"
  "flight".startsWith("flo")? No → prefix = "fl"
  "flight".startsWith("fl")? Yes → stop

Answer: "fl" ✓
```

---

## Problem 2: Reverse Words in a String

**Problem:** Reverse the order of words in a string. Words are separated by spaces.

**Input:** `"the sky is blue"`
**Output:** `"blue is sky the"`

**Input:** `"  hello world  "` (extra spaces)
**Output:** `"world hello"` (no extra spaces in output)

```javascript
function reverseWords(s) {
  return s.trim()           // remove leading/trailing spaces
    .split(/\s+/)           // split by one or more spaces
    .reverse()              // reverse the array of words
    .join(" ");             // join with single space
}

console.log(reverseWords("the sky is blue"));    // "blue is sky the"
console.log(reverseWords("  hello world  "));    // "world hello"
console.log(reverseWords("a good   example"));   // "example good a"
```

---

## Problem 3: String to Integer (atoi)

**Problem:** Convert a string to an integer. Handle:
- Leading spaces (skip them)
- Optional `+` or `-` sign
- Digits until a non-digit is found
- Overflow (if number exceeds 32-bit integer range)

**Input:** `"  -42"` → `-42`
**Input:** `"4193 with words"` → `4193`
**Input:** `"words and 987"` → `0` (starts with non-digit)

```javascript
function myAtoi(s) {
  const MAX = 2 ** 31 - 1;   // 2147483647
  const MIN = -(2 ** 31);    // -2147483648

  let i = 0;
  let sign = 1;
  let result = 0;

  // Step 1: skip leading spaces
  while (i < s.length && s[i] === " ") i++;

  // Step 2: optional sign
  if (i < s.length && (s[i] === "+" || s[i] === "-")) {
    if (s[i] === "-") sign = -1;
    i++;
  }

  // Step 3: read digits
  while (i < s.length && s[i] >= "0" && s[i] <= "9") {
    const digit = s[i].charCodeAt(0) - "0".charCodeAt(0);
    result = result * 10 + digit;

    // Check overflow before multiplying further
    if (result * sign > MAX) return MAX;
    if (result * sign < MIN) return MIN;

    i++;
  }

  return result * sign;
}

console.log(myAtoi("  -42"));          // -42
console.log(myAtoi("4193 with words")); // 4193
console.log(myAtoi("words and 987"));   // 0
console.log(myAtoi("+-12"));           // 0 (invalid)
```

---

## Problem 4: Decode String

**Problem:** A string is encoded as `k[s]` meaning the string `s` is repeated `k` times.

**Input:** `"3[a]2[bc]"`
**Output:** `"aaabcbc"`

**Input:** `"3[a2[c]]"`
**Output:** `"accaccacc"`

This is a classic **Stack** problem — but we preview it here.

**Idea:**
- When you see a number, remember it
- When you see `[`, push current state onto a stack
- When you see `]`, pop state and repeat the current string

```javascript
function decodeString(s) {
  const stack = [];     // stack of [currentString, repeatCount]
  let currentStr = "";
  let currentNum = 0;

  for (const char of s) {
    if (char >= "0" && char <= "9") {
      currentNum = currentNum * 10 + parseInt(char);  // handle multi-digit numbers
    } else if (char === "[") {
      stack.push([currentStr, currentNum]);  // save state
      currentStr = "";
      currentNum = 0;
    } else if (char === "]") {
      const [prevStr, repeatCount] = stack.pop();  // restore state
      currentStr = prevStr + currentStr.repeat(repeatCount);
    } else {
      currentStr += char;
    }
  }

  return currentStr;
}

console.log(decodeString("3[a]2[bc]"));  // "aaabcbc"
console.log(decodeString("3[a2[c]]"));   // "accaccacc"
console.log(decodeString("2[abc]3[cd]ef"));  // "abcabccdcdcdef"
```

---

## Pattern Summary

Here are all the string patterns and which technique to use:

| Pattern | Technique | Example Problem |
|---------|-----------|----------------|
| Is it a palindrome? | Two pointers | isPalindrome |
| Longest palindromic substring | Expand from center | longestPalindrome |
| Count characters / is anagram? | Frequency Map | isAnagram |
| Longest substring without X | Sliding window + Set/Map | longestUniqueSubstring |
| Find all anagrams | Fixed sliding window + Map | findAnagrams |
| Minimum window with all chars | Variable sliding window + Map | minWindow |
| Reverse words | split + reverse + join | reverseWords |
| Parse/process character by character | Simple loop | atoi, compress |
| Brackets / nesting | Stack (Phase 6) | isBalanced, decodeString |
| Longest common prefix | Shrink until match | longestCommonPrefix |

---

## Phase 4 Self-Check

Can you answer these without looking?

- [ ] What is the difference between a substring and a subsequence?
- [ ] Why are strings immutable in JavaScript — and what do you do when you need to modify one?
- [ ] How do you check if a string is a palindrome (two ways)?
- [ ] How do you check if two strings are anagrams?
- [ ] What technique do you use for "longest substring with property X"?
- [ ] How do you reverse words in a string?
- [ ] What does `s.split("").reverse().join("")` do?

---

## Quick Reference: String ↔ Array Conversions

```javascript
// String → Array of characters
"hello".split("")         // ["h","e","l","l","o"]

// String → Array of words
"hello world".split(" ")  // ["hello", "world"]

// Array of characters → String
["h","e","l","l","o"].join("")  // "hello"

// Array of words → String
["hello", "world"].join(" ")    // "hello world"

// Reverse a string (3-step)
"hello".split("").reverse().join("")  // "olleh"
```

---

**Next: Day 20 — Linked Lists: What They Are**
Phase 5 begins. A linked list is a completely different way to store a sequence of items — instead of a contiguous block in memory, each item points to the next. This allows O(1) insert/delete at the front (unlike arrays), but loses O(1) index access.
