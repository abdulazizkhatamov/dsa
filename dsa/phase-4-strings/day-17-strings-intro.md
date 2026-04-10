# Day 17 — Strings: What They Are and Common Patterns

---

## What is a String?

A string is a **sequence of characters** — letters, numbers, spaces, symbols.

```javascript
let name = "Ali";
let greeting = "Hello, World!";
let empty = "";
```

Under the hood, a string is stored just like an array — each character sits in a numbered slot:

```
"Ali"
index:  0    1    2
char:  'A'  'l'  'i'
```

So many array concepts apply to strings:
- You can access by index: `"Ali"[0]` → `'A'`
- You can get the length: `"Ali".length` → `3`
- Searching is O(n)

But strings in JavaScript are **immutable** — you can't change a character in-place. You must create a new string.

```javascript
let s = "hello";
s[0] = "H";        // does nothing in JavaScript
console.log(s);    // "hello" — unchanged!

// To modify, create a new string:
s = "H" + s.slice(1);   // "Hello"
```

---

## Essential String Methods

```javascript
const s = "Hello, World!";

// Length
s.length;              // 13

// Access character
s[0];                  // "H"
s.charAt(0);           // "H"
s.at(-1);              // "!"  (last character)

// Check if contains
s.includes("World");   // true
s.includes("xyz");     // false

// Find index
s.indexOf("o");        // 4  (first 'o')
s.lastIndexOf("o");    // 8  (last 'o')

// Slice (extract part)
s.slice(7, 12);        // "World"
s.slice(0, 5);         // "Hello"
s.slice(-6);           // "orld!"  (from end)

// Case
s.toLowerCase();       // "hello, world!"
s.toUpperCase();       // "HELLO, WORLD!"

// Trim whitespace
"  hello  ".trim();    // "hello"
"  hello  ".trimStart(); // "hello  "
"  hello  ".trimEnd();   // "  hello"

// Split into array
"a,b,c".split(",");    // ["a", "b", "c"]
"hello".split("");     // ["h","e","l","l","o"]  (every character)
"hello".split(" ");    // ["hello"]  (no spaces to split on)

// Replace
"hello".replace("l", "r");      // "herlo"  (first only)
"hello".replaceAll("l", "r");   // "herro"  (all occurrences)

// Check start/end
"hello".startsWith("he");  // true
"hello".endsWith("lo");    // true
```

---

## Iterating Through a String

```javascript
const s = "hello";

// Way 1: by index
for (let i = 0; i < s.length; i++) {
  console.log(i, s[i]);
  // 0 h, 1 e, 2 l, 3 l, 4 o
}

// Way 2: for...of (gives characters directly)
for (const char of s) {
  console.log(char);  // h, e, l, l, o
}
```

---

## Converting Between String and Array

In many string problems, you need to modify characters. Since strings are immutable, the pattern is:
1. Convert to array
2. Modify the array
3. Join back into string

```javascript
// String → Array
const chars = "hello".split("");  // ["h","e","l","l","o"]

// Modify
chars[0] = "H";

// Array → String
const result = chars.join("");  // "Hello"
```

---

## Problem 1: Reverse a String

**Input:** `"hello"`
**Output:** `"olleh"`

```javascript
function reverseString(s) {
  return s.split("").reverse().join("");
}

// Or manually with two pointers:
function reverseString(s) {
  const chars = s.split("");
  let left = 0;
  let right = chars.length - 1;

  while (left < right) {
    [chars[left], chars[right]] = [chars[right], chars[left]];  // swap
    left++;
    right--;
  }

  return chars.join("");
}

console.log(reverseString("hello"));   // "olleh"
console.log(reverseString("abcde"));   // "edcba"
```

The `[a, b] = [b, a]` syntax is JavaScript's **destructuring swap** — it swaps two values in one line without a temp variable.

---

## Problem 2: Count Vowels

**Input:** `"hello world"`
**Output:** `3` (e, o, o)

```javascript
function countVowels(s) {
  const vowels = new Set(['a', 'e', 'i', 'o', 'u']);
  let count = 0;

  for (const char of s.toLowerCase()) {
    if (vowels.has(char)) count++;
  }

  return count;
}

console.log(countVowels("hello world"));  // 3
console.log(countVowels("AEIOU"));        // 5
```

---

## Problem 3: Compress a String (Run-Length Encoding)

**Problem:** Compress a string by replacing consecutive repeated characters with the character followed by its count. If the compressed string is not shorter, return the original.

**Input:** `"aaabbbccdddddd"`
**Output:** `"a3b3c2d6"`

**Input:** `"abc"`
**Output:** `"abc"` (compressed "a1b1c1" is longer — return original)

```javascript
function compress(s) {
  let result = "";
  let i = 0;

  while (i < s.length) {
    const char = s[i];
    let count = 0;

    // Count consecutive same characters
    while (i < s.length && s[i] === char) {
      count++;
      i++;
    }

    result += char + (count > 1 ? count : "");  // only add count if > 1
  }

  return result.length < s.length ? result : s;
}

console.log(compress("aaabbbccdddddd"));  // "a3b3c2d6"
console.log(compress("aab"));            // "a2b"
console.log(compress("abc"));            // "abc" (not shorter)
```

---

## Problem 4: Check if Parentheses are Balanced

**Problem:** Given a string with `(`, `)`, `[`, `]`, `{`, `}`, check if the brackets are properly opened and closed.

**Input:** `"()[]{}"` → `true`
**Input:** `"([)]"` → `false`
**Input:** `"{[]}"` → `true`

This is a **Stack** problem (we'll cover stacks in Phase 6), but it's so common with strings that we'll preview it here.

**Idea:**
- When you see an opening bracket: push it onto a stack
- When you see a closing bracket: pop from stack, check it matches

```javascript
function isBalanced(s) {
  const stack = [];
  const matching = { ')': '(', ']': '[', '}': '{' };

  for (const char of s) {
    if ("([{".includes(char)) {
      stack.push(char);  // opening bracket — push
    } else {
      // Closing bracket — check it matches the last opened
      if (stack.length === 0 || stack[stack.length - 1] !== matching[char]) {
        return false;
      }
      stack.pop();
    }
  }

  return stack.length === 0;  // true if all brackets were matched
}

console.log(isBalanced("()[]{}"));  // true
console.log(isBalanced("([)]"));    // false
console.log(isBalanced("{[]}"));    // true
console.log(isBalanced("("));       // false  (unclosed)
```

---

## The Most Important String + Sliding Window Pattern

The pattern for "longest substring with some property" uses:
- Sliding window (variable size)
- A Map or Set to track window contents

You already saw this in Day 11 with "Longest Substring Without Repeating Characters".

General template:
```javascript
function longestSubstringWith(s, condition) {
  let left = 0;
  let maxLength = 0;
  const windowState = new Map();  // or Set

  for (let right = 0; right < s.length; right++) {
    // Add s[right] to window state
    addToWindow(s[right], windowState);

    // Shrink while condition violated
    while (conditionViolated(windowState)) {
      removeFromWindow(s[left], windowState);
      left++;
    }

    maxLength = Math.max(maxLength, right - left + 1);
  }

  return maxLength;
}
```

---

## Summary

| Concept | Key Point |
|---------|-----------|
| Strings are immutable | Can't modify in-place — must create new string |
| Access | `s[i]` or `s.charAt(i)` — O(1) |
| Search | `indexOf`, `includes` — O(n) |
| Convert to array | `s.split("")` — to enable in-place tricks |
| Join back | `arr.join("")` |
| Common methods | `slice`, `toLowerCase`, `trim`, `replace`, `startsWith`, `endsWith` |

---

## Practice Problems

**Practice 1:** Write a function that returns true if a string contains all unique characters.
```
Input: "abcde"  → true
Input: "hello"  → false  ('l' repeats)
```

**Practice 2:** Write a function that counts how many words are in a sentence.
```
Input: "the quick brown fox"
Output: 4
```

---

## Answers

**Practice 1:**
```javascript
function allUnique(s) {
  return new Set(s).size === s.length;
  // Set removes duplicates. If size equals length, no duplicates existed.
}

console.log(allUnique("abcde"));  // true
console.log(allUnique("hello"));  // false
```

**Practice 2:**
```javascript
function countWords(s) {
  return s.trim().split(/\s+/).length;
  // trim removes leading/trailing spaces
  // \s+ splits on one or more whitespace characters
}

console.log(countWords("the quick brown fox"));   // 4
console.log(countWords("  hello   world  "));     // 2
```

---

**Next: Day 18 — Strings: Palindromes and Anagrams**
Classic interview problems using the patterns you already know.
