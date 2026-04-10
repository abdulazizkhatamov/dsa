# Day 33 — Binary Search

---

## What is Binary Search?

Binary search finds an element in a **sorted** array by repeatedly halving the search space.

Instead of checking every element (O(n) linear search), it:
1. Looks at the MIDDLE element
2. If middle === target → found!
3. If target < middle → look in LEFT half (discard right half)
4. If target > middle → look in RIGHT half (discard left half)
5. Repeat

Each step cuts the remaining search space in half. After log₂(n) steps, you've either found it or confirmed it's not there.

For 1,000,000 elements: at most 20 steps. O(log n).

---

## The Phone Book Analogy

Imagine finding "Smith" in a phone book:
- Open to the middle: "M"
- "Smith" comes after M → ignore first half
- Open to middle of second half: "R"
- "Smith" comes after R → ignore that half too
- Open to middle of remaining: "T"
- "Smith" comes before T → ignore right side
- ...continue until found

You never start from page 1 and check every name.

---

## Basic Binary Search

```javascript
function binarySearch(arr, target) {
  let left = 0;
  let right = arr.length - 1;

  while (left <= right) {
    const mid = Math.floor((left + right) / 2);

    if (arr[mid] === target) {
      return mid;              // found — return index
    } else if (arr[mid] < target) {
      left = mid + 1;          // target is in right half
    } else {
      right = mid - 1;         // target is in left half
    }
  }

  return -1;  // not found
}

console.log(binarySearch([1, 3, 5, 7, 9, 11, 13], 7));   // 3
console.log(binarySearch([1, 3, 5, 7, 9, 11, 13], 6));   // -1
console.log(binarySearch([1, 3, 5, 7, 9, 11, 13], 1));   // 0
console.log(binarySearch([1, 3, 5, 7, 9, 11, 13], 13));  // 6
```

**Trace finding 7 in [1,3,5,7,9,11,13]:**
```
left=0, right=6, mid=3, arr[3]=7 === 7 → return 3 ✓ (found in 1 step!)
```

**Trace finding 6 in [1,3,5,7,9,11,13]:**
```
left=0, right=6, mid=3, arr[3]=7 > 6 → right=2
left=0, right=2, mid=1, arr[1]=3 < 6 → left=2
left=2, right=2, mid=2, arr[2]=5 < 6 → left=3
left=3 > right=2 → exit loop → return -1 ✓
```

---

## The Off-By-One Trap

Binary search has subtle bugs if you're not careful with the boundaries. Remember:

- The loop condition is `left <= right` (not `left < right`)
- When target < mid: `right = mid - 1` (not `mid`)
- When target > mid: `left = mid + 1` (not `mid`)

If you use `right = mid` or `left = mid`, you can get stuck in an infinite loop.

---

## Finding the First / Last Occurrence

What if the array has duplicates and you need the **first** position of the target?

**Input:** `[1, 2, 2, 2, 3]`, target = 2
**Output:** `1` (first index of 2, not 2 or 3)

```javascript
function firstOccurrence(arr, target) {
  let left = 0;
  let right = arr.length - 1;
  let result = -1;

  while (left <= right) {
    const mid = Math.floor((left + right) / 2);

    if (arr[mid] === target) {
      result = mid;       // record this as a potential answer
      right = mid - 1;   // but keep looking LEFT for earlier occurrence
    } else if (arr[mid] < target) {
      left = mid + 1;
    } else {
      right = mid - 1;
    }
  }

  return result;
}

function lastOccurrence(arr, target) {
  let left = 0;
  let right = arr.length - 1;
  let result = -1;

  while (left <= right) {
    const mid = Math.floor((left + right) / 2);

    if (arr[mid] === target) {
      result = mid;      // record answer
      left = mid + 1;   // keep looking RIGHT for later occurrence
    } else if (arr[mid] < target) {
      left = mid + 1;
    } else {
      right = mid - 1;
    }
  }

  return result;
}

console.log(firstOccurrence([1, 2, 2, 2, 3], 2));  // 1
console.log(lastOccurrence([1, 2, 2, 2, 3], 2));   // 3
```

---

## Finding the Insertion Point

**Problem:** In a sorted array, find where you would insert `target` to keep the array sorted.

**Input:** `[1, 3, 5, 6]`, target=5 → Output: 2 (already there)
**Input:** `[1, 3, 5, 6]`, target=2 → Output: 1 (between 1 and 3)
**Input:** `[1, 3, 5, 6]`, target=7 → Output: 4 (after all elements)

```javascript
function searchInsertPosition(arr, target) {
  let left = 0;
  let right = arr.length - 1;

  while (left <= right) {
    const mid = Math.floor((left + right) / 2);

    if (arr[mid] === target) return mid;
    if (arr[mid] < target) left = mid + 1;
    else right = mid - 1;
  }

  return left;  // when loop ends, left is the insertion point
}

console.log(searchInsertPosition([1, 3, 5, 6], 5));  // 2
console.log(searchInsertPosition([1, 3, 5, 6], 2));  // 1
console.log(searchInsertPosition([1, 3, 5, 6], 7));  // 4
console.log(searchInsertPosition([1, 3, 5, 6], 0));  // 0
```

---

## Binary Search on the Answer

One powerful application: binary search doesn't just work on arrays. You can binary search on the **answer space** when:
- The answer is a number in some range
- You can check: "Is X a valid answer?" in O(n) or less

**Problem:** Find the square root of n (integer part only).
- Range: answer is between 1 and n
- Validity check: `mid * mid <= n`

```javascript
function mySqrt(n) {
  if (n === 0) return 0;

  let left = 1;
  let right = n;
  let result = 1;

  while (left <= right) {
    const mid = Math.floor((left + right) / 2);

    if (mid * mid === n) return mid;
    if (mid * mid < n) {
      result = mid;       // mid is a valid answer, but maybe larger exists
      left = mid + 1;
    } else {
      right = mid - 1;
    }
  }

  return result;
}

console.log(mySqrt(4));    // 2
console.log(mySqrt(8));    // 2  (floor of 2.828...)
console.log(mySqrt(25));   // 5
console.log(mySqrt(100));  // 10
```

---

## Summary

Binary search requires:
1. **Sorted** input (or a monotonic condition)
2. **Random access** (works great for arrays)

The template:
```javascript
let left = 0, right = arr.length - 1;
while (left <= right) {
  const mid = Math.floor((left + right) / 2);
  if (arr[mid] === target) return mid;          // found
  else if (arr[mid] < target) left = mid + 1;  // search right
  else right = mid - 1;                         // search left
}
return -1;  // not found
```

| Variant | Change |
|---------|--------|
| Basic | Return index or -1 |
| First occurrence | When found, keep `right = mid - 1` |
| Last occurrence | When found, keep `left = mid + 1` |
| Insertion position | Return `left` when loop ends |
| Answer space | Binary search the range of valid answers |

---

## Practice Problems

**Practice 1:** Given a sorted array rotated at an unknown point, find the target.
```
Input: arr = [4, 5, 6, 7, 0, 1, 2], target = 0
Output: 4  (index of 0)
```
*(Hint: one half is always sorted — determine which half, check if target is in it)*

**Practice 2:** Find the minimum element in a rotated sorted array.
```
Input: [3, 4, 5, 1, 2]
Output: 1
```

---

## Answers

**Practice 1:**
```javascript
function searchRotated(arr, target) {
  let left = 0, right = arr.length - 1;

  while (left <= right) {
    const mid = Math.floor((left + right) / 2);
    if (arr[mid] === target) return mid;

    // Left half is sorted
    if (arr[left] <= arr[mid]) {
      if (arr[left] <= target && target < arr[mid]) {
        right = mid - 1;  // target is in sorted left half
      } else {
        left = mid + 1;   // target is in right half
      }
    } else {
      // Right half is sorted
      if (arr[mid] < target && target <= arr[right]) {
        left = mid + 1;   // target is in sorted right half
      } else {
        right = mid - 1;  // target is in left half
      }
    }
  }

  return -1;
}

console.log(searchRotated([4, 5, 6, 7, 0, 1, 2], 0));  // 4
console.log(searchRotated([4, 5, 6, 7, 0, 1, 2], 3));  // -1
```

**Practice 2:**
```javascript
function findMin(arr) {
  let left = 0, right = arr.length - 1;

  while (left < right) {
    const mid = Math.floor((left + right) / 2);

    if (arr[mid] > arr[right]) {
      left = mid + 1;   // minimum is in right half
    } else {
      right = mid;      // minimum is in left half (or is mid)
    }
  }

  return arr[left];
}

console.log(findMin([3, 4, 5, 1, 2]));  // 1
console.log(findMin([4, 5, 6, 7, 0, 1, 2]));  // 0
```

---

**Next: Day 34 — Searching: More Binary Search Problems**
Applying binary search to more complex scenarios, including 2D matrices and "search the answer space" problems.
