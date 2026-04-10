# Day 14 — Hash Maps: What They Are and How They Work

---

## The Problem They Solve

Imagine you have a list of 1,000,000 students. You need to check if "Ali" is in the list.

With an array, you'd have to look at every student one by one. Worst case: you check all 1,000,000 before finding Ali (or confirming he's not there). That's O(n).

**A Hash Map can check this in O(1) — instantly, no matter how many students there are.**

---

## What is a Hash Map?

A Hash Map is a data structure that stores **key → value pairs**.

Think of it like a physical dictionary:
- You look up a **word** (the key)
- You instantly find the **definition** (the value)

You don't read every word from A to Z. You just go straight to the right page.

```javascript
// Key → Value
"Ali"   → "student ID: 1042"
"Sara"  → "student ID: 2091"
"John"  → "student ID: 3314"
```

In JavaScript, a Hash Map is the **`Map`** object (or plain objects `{}`).

---

## How to Use a Map in JavaScript

```javascript
// Create a Map
const map = new Map();

// Add key-value pairs
map.set("Ali", 1042);
map.set("Sara", 2091);
map.set("John", 3314);

// Get a value by key
console.log(map.get("Ali"));    // 1042
console.log(map.get("Sara"));   // 2091
console.log(map.get("xyz"));    // undefined (key doesn't exist)

// Check if a key exists
console.log(map.has("John"));   // true
console.log(map.has("Bob"));    // false

// Remove a key
map.delete("John");
console.log(map.has("John"));   // false

// Size of the map
console.log(map.size);          // 2 (John was deleted)
```

---

## Big O for Map Operations

| Operation | Big O | Example |
|-----------|-------|---------|
| `map.set(key, value)` | O(1) | Add/update a key |
| `map.get(key)` | O(1) | Look up a value |
| `map.has(key)` | O(1) | Check if key exists |
| `map.delete(key)` | O(1) | Remove a key |
| Iterating all keys | O(n) | Looping through map |

This is what makes Hash Maps so powerful — every core operation is O(1).

---

## How Does It Actually Work? (The "Hash" Part)

This is how the magic works internally (you don't need to implement this — just understand it).

When you do `map.set("Ali", 1042)`, the Map does this internally:

1. Takes the key `"Ali"`
2. Runs it through a **hash function** — a formula that converts the key into a number
3. That number becomes an **index** in an internal array
4. Stores the value at that index

```
"Ali"  → hash function → 3   → store 1042 at index 3
"Sara" → hash function → 7   → store 2091 at index 7
"John" → hash function → 1   → store 3314 at index 1
```

When you look up `map.get("Ali")`:
1. Run "Ali" through the same hash function → 3
2. Go to index 3 in the internal array
3. Return the value there → 1042

It's like having a formula that tells you exactly which drawer to open.

---

## Map vs Plain Object `{}`

In JavaScript, you can also use a plain object as a key-value store:

```javascript
// Plain object
const obj = {};
obj["Ali"] = 1042;
obj["Sara"] = 2091;
console.log(obj["Ali"]);  // 1042
console.log("Ali" in obj);  // true
```

**When to use Map vs object:**
- Use `Map` when keys can be any type (numbers, objects, etc.)
- Use `Map` when you need to frequently add/delete keys
- Use plain `{}` for simple cases with string keys
- In DSA problems, both work — use whichever feels natural

---

## The Most Common Use: Counting Frequencies

The #1 use of Hash Maps in DSA is **counting how many times something appears**.

**Problem:** Count how many times each character appears in a string.

**Input:** `"banana"`
**Output:** `{b: 1, a: 3, n: 2}`

```javascript
function charFrequency(str) {
  const freq = new Map();

  for (const char of str) {
    if (freq.has(char)) {
      freq.set(char, freq.get(char) + 1);  // already seen: increment
    } else {
      freq.set(char, 1);                   // first time: set to 1
    }
  }

  return freq;
}

const result = charFrequency("banana");
console.log(result.get("a"));  // 3
console.log(result.get("b"));  // 1
console.log(result.get("n"));  // 2
```

**Shorter way** (common shortcut):
```javascript
function charFrequency(str) {
  const freq = new Map();

  for (const char of str) {
    freq.set(char, (freq.get(char) || 0) + 1);
    // If char exists, increment. If not (undefined), start at 0, then +1.
  }

  return freq;
}
```

The `(freq.get(char) || 0)` trick:
- `freq.get(char)` returns the current count (or `undefined` if not there yet)
- `undefined || 0` = `0` (since undefined is falsy)
- Then `+ 1` = start at 1

---

## Iterating Through a Map

```javascript
const map = new Map();
map.set("a", 3);
map.set("b", 1);
map.set("n", 2);

// Loop through entries (key and value)
for (const [key, value] of map) {
  console.log(key, value);
  // a 3
  // b 1
  // n 2
}

// Just keys
for (const key of map.keys()) {
  console.log(key);  // a, b, n
}

// Just values
for (const value of map.values()) {
  console.log(value);  // 3, 1, 2
}
```

---

## The Set: Hash Map's Simpler Sibling

You've already used `Set` in previous days. A Set is like a Map, but it only stores **keys** — no values.

```javascript
const seen = new Set();
seen.add("Ali");
seen.add("Sara");
seen.add("Ali");   // duplicate — ignored

console.log(seen.size);         // 2 (not 3)
console.log(seen.has("Ali"));   // true
console.log(seen.has("Bob"));   // false
seen.delete("Ali");
console.log(seen.has("Ali"));   // false
```

Use a **Set** when you only care if something exists (yes/no).
Use a **Map** when you also need to store a value alongside the key.

---

## Summary

| Feature | Map | Set |
|---------|-----|-----|
| Stores | key + value | key only |
| Access | `map.get(key)` | `set.has(key)` |
| Add | `map.set(k, v)` | `set.add(k)` |
| Remove | `map.delete(k)` | `set.delete(k)` |
| All operations | O(1) | O(1) |
| Use for | Counting, lookups | Existence checks, deduplication |

**The key insight:** Hash Maps give you O(1) lookups by converting keys into array indexes via a hash function. This turns many O(n²) problems into O(n).

---

## Practice Problems

**Practice 1:** Write a function that returns the most frequent element in an array.
```
Input: [1, 3, 2, 3, 1, 3]
Output: 3  (appears 3 times)
```

**Practice 2:** Write a function that returns true if two arrays contain the same elements (ignoring order).
```
Input: [1, 2, 3], [3, 1, 2]
Output: true

Input: [1, 2, 3], [1, 2, 4]
Output: false
```

---

## Answers

**Practice 1:**
```javascript
function mostFrequent(arr) {
  const freq = new Map();
  for (const num of arr) {
    freq.set(num, (freq.get(num) || 0) + 1);
  }

  let maxCount = 0;
  let result = null;
  for (const [num, count] of freq) {
    if (count > maxCount) {
      maxCount = count;
      result = num;
    }
  }

  return result;
}

console.log(mostFrequent([1, 3, 2, 3, 1, 3]));  // 3
```

**Practice 2:**
```javascript
function sameElements(arr1, arr2) {
  if (arr1.length !== arr2.length) return false;

  const freq = new Map();
  for (const num of arr1) {
    freq.set(num, (freq.get(num) || 0) + 1);
  }

  for (const num of arr2) {
    if (!freq.has(num) || freq.get(num) === 0) return false;
    freq.set(num, freq.get(num) - 1);
  }

  return true;
}

console.log(sameElements([1, 2, 3], [3, 1, 2]));  // true
console.log(sameElements([1, 2, 3], [1, 2, 4]));  // false
```

---

**Next: Day 15 — Hash Maps: Frequency Counter Pattern**
The most important pattern using Hash Maps — used in dozens of interview problems.
