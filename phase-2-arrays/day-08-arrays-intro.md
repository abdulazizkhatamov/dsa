# Day 8 — Arrays: What They Are and How They Work in Memory

---

## What is an Array?

An array is an **ordered list of items** stored in a row in your computer's memory.

Think of it like a row of lockers at a school:
```
Locker:  [0]     [1]     [2]     [3]     [4]
Content: "Ali"  "Sara"  "John"  "Bob"   "Mia"
```

Each locker has a number — that is called the **index**.
Indexes always start at **0** (not 1 — this trips people up constantly).

```javascript
let names = ["Ali", "Sara", "John", "Bob", "Mia"];
//  index:     0       1       2       3      4
```

---

## How Arrays Are Stored in Memory

Your computer's memory (RAM) is like a very long street with numbered houses.
Each house can hold one small piece of data.

When you create an array, the computer finds **a row of empty houses next to each other** and stores your items there:

```
Memory address:  100    101    102    103    104
Content:         "Ali"  "Sara" "John" "Bob"  "Mia"
```

This is why accessing any element by index is **O(1)** (instant).

The computer knows:
- Array starts at address 100
- Each item takes 1 spot
- `names[3]` = address 100 + 3 = address 103 = "Bob"

It calculates the exact address instantly. No searching needed.

---

## Basic Array Operations

### Creating an array
```javascript
let empty = [];                     // empty array
let numbers = [1, 2, 3, 4, 5];     // array with numbers
let mixed = ["hello", 42, true];    // arrays can hold different types
```

### Accessing elements — O(1)
```javascript
let fruits = ["apple", "banana", "cherry"];

console.log(fruits[0]);  // "apple"   — first item
console.log(fruits[1]);  // "banana"
console.log(fruits[2]);  // "cherry"  — last item

// Negative index trick (modern JS):
console.log(fruits.at(-1));  // "cherry" — last item
console.log(fruits.at(-2));  // "banana" — second to last
```

### Getting the length
```javascript
let fruits = ["apple", "banana", "cherry"];
console.log(fruits.length);  // 3

// Last index is always length - 1
console.log(fruits[fruits.length - 1]);  // "cherry"
```

---

## Adding and Removing — and Why Position Matters

### Add to the END — O(1) (fast)
```javascript
let arr = [1, 2, 3];
arr.push(4);
// arr is now [1, 2, 3, 4]
```

Why O(1)? The computer just puts the new item in the next empty memory spot after the array. No shifting needed.

### Remove from the END — O(1) (fast)
```javascript
let arr = [1, 2, 3, 4];
let removed = arr.pop();
// removed = 4
// arr is now [1, 2, 3]
```

### Add to the BEGINNING — O(n) (slow!)
```javascript
let arr = [1, 2, 3];
arr.unshift(0);
// arr is now [0, 1, 2, 3]
```

Why O(n)? Because items must be **shifted** to make room at position 0.

Before:
```
index:    0    1    2
item:     1    2    3
```

To add 0 at the beginning, every existing item shifts one position to the right:
```
index:    0    1    2    3
item:     0    1    2    3
          ↑
       new item, everything else moved right
```

That's 3 shifts for 3 items. For n items, n shifts → O(n).

### Remove from the BEGINNING — O(n) (slow!)
```javascript
let arr = [0, 1, 2, 3];
let removed = arr.shift();
// removed = 0
// arr is now [1, 2, 3]
// every item shifted one position left — O(n)
```

### Insert or remove in the MIDDLE — O(n) (slow!)
```javascript
let arr = [1, 2, 3, 4, 5];

// splice(startIndex, deleteCount, ...itemsToInsert)
arr.splice(2, 0, 99);  // at index 2, delete 0 items, insert 99
// arr is now [1, 2, 99, 3, 4, 5]
// items at index 2,3,4 had to shift right → O(n)
```

---

## Big O Summary for Arrays

| Operation | Big O | Why |
|-----------|-------|-----|
| Access by index `arr[i]` | O(1) | Direct calculation of memory address |
| Search (find a value) | O(n) | May need to check every element |
| Push (add to end) | O(1) | Just add after last item |
| Pop (remove from end) | O(1) | Just remove last item |
| Unshift (add to beginning) | O(n) | Must shift all items |
| Shift (remove from beginning) | O(n) | Must shift all items |
| Splice (insert/remove middle) | O(n) | Must shift items |

---

## Iterating Through an Array

There are multiple ways to loop through an array in JavaScript. You'll use all of these:

```javascript
let numbers = [10, 20, 30, 40, 50];

// Way 1: classic for loop — gives you index and value
for (let i = 0; i < numbers.length; i++) {
  console.log(i, numbers[i]);
  // 0  10
  // 1  20
  // 2  30
  // 3  40
  // 4  50
}

// Way 2: for...of — gives you just the value (clean, simple)
for (const num of numbers) {
  console.log(num);  // 10, 20, 30, 40, 50
}

// Way 3: forEach — also just values (functional style)
numbers.forEach((num, index) => {
  console.log(index, num);
});
```

For DSA problems, you'll use **Way 1** most often because you frequently need the index.

---

## Important Array Methods You Must Know

These are all O(n) — they look at every element:

```javascript
let arr = [3, 1, 4, 1, 5, 9, 2, 6];

// Find index of a value (-1 if not found)
arr.indexOf(4);      // 2  (first position of 4)
arr.indexOf(99);     // -1 (not found)

// Check if value exists
arr.includes(5);     // true
arr.includes(99);    // false

// Create new arrays (non-destructive — don't change original):
arr.slice(2, 5);     // [4, 1, 5]  — elements from index 2 to 4
arr.slice(0, 3);     // [3, 1, 4]
arr.slice(-2);       // [2, 6]     — last 2 elements

// Transform to new array:
arr.map(x => x * 2);  // [6, 2, 8, 2, 10, 18, 4, 12]  — each element × 2

// Filter to new array:
arr.filter(x => x > 3);  // [4, 5, 9, 6]  — only elements > 3

// Reduce to one value:
arr.reduce((sum, x) => sum + x, 0);  // 31  — sum of all elements

// Sort (changes the original array!):
arr.sort((a, b) => a - b);  // [1, 1, 2, 3, 4, 5, 6, 9]  — ascending
arr.sort((a, b) => b - a);  // [9, 6, 5, 4, 3, 2, 1, 1]  — descending
```

---

## Multi-Dimensional Arrays (2D Arrays)

An array can contain other arrays. This creates a "grid" or "matrix".

```javascript
let grid = [
  [1, 2, 3],    // row 0
  [4, 5, 6],    // row 1
  [7, 8, 9]     // row 2
];

// Access with two indexes: grid[row][column]
console.log(grid[0][0]);  // 1  (row 0, column 0)
console.log(grid[1][2]);  // 6  (row 1, column 2)
console.log(grid[2][1]);  // 8  (row 2, column 1)

// Loop through a 2D array:
for (let i = 0; i < grid.length; i++) {       // rows
  for (let j = 0; j < grid[i].length; j++) { // columns
    console.log(grid[i][j]);
  }
}
```

This is O(n × m) — where n = number of rows, m = number of columns.

---

## Summary

- An array is an ordered list of items stored in memory next to each other
- Index starts at 0
- Access by index is O(1) — instant
- Adding/removing from the END is O(1) — fast
- Adding/removing from the BEGINNING or MIDDLE is O(n) — slow (shifting needed)
- Searching through the array is O(n) — may need to check all elements

---

**Next: Day 9 — Array Methods and Their Big O**
We'll go deeper on the methods and start using arrays to solve real problems.
