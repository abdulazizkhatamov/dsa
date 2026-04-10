# Day 29 — Sorting: Bubble Sort and Selection Sort

---

## Why Learn Sorting?

JavaScript has `arr.sort()` built in. So why learn sorting algorithms?

1. Interviews ask you to implement them — they test if you understand how algorithms work
2. Understanding sorting helps you choose the right approach for different problems
3. Sorting concepts (comparing, swapping, partitioning) appear in many other algorithms
4. `arr.sort()` has limitations (sorts alphabetically by default, need a comparator for numbers)

```javascript
// JavaScript's built-in sort — IMPORTANT TRAP
[10, 2, 30, 1].sort()        // [1, 10, 2, 30] — WRONG! Sorts as strings
[10, 2, 30, 1].sort((a, b) => a - b)  // [1, 2, 10, 30] — correct
```

---

## Bubble Sort

**Idea:** Repeatedly step through the array, compare adjacent elements, and swap them if they're in the wrong order. After each full pass, the largest unsorted element "bubbles" to its correct position at the end.

```
arr = [5, 3, 1, 4, 2]

Pass 1:
[5,3,1,4,2] → compare 5,3: swap → [3,5,1,4,2]
[3,5,1,4,2] → compare 5,1: swap → [3,1,5,4,2]
[3,1,5,4,2] → compare 5,4: swap → [3,1,4,5,2]
[3,1,4,5,2] → compare 5,2: swap → [3,1,4,2,5]
5 is now in its correct final position ✓

Pass 2:
[3,1,4,2,5] → compare 3,1: swap → [1,3,4,2,5]
[1,3,4,2,5] → compare 3,4: ok  → [1,3,4,2,5]
[1,3,4,2,5] → compare 4,2: swap → [1,3,2,4,5]
4 and 5 are in correct positions ✓

Pass 3:
[1,3,2,4,5] → compare 1,3: ok
[1,3,2,4,5] → compare 3,2: swap → [1,2,3,4,5]
3,4,5 correct ✓

Pass 4:
[1,2,3,4,5] → compare 1,2: ok
1,2,3,4,5 all correct ✓
```

```javascript
function bubbleSort(arr) {
  const n = arr.length;

  for (let i = 0; i < n - 1; i++) {
    // After each pass, the last (i) elements are sorted
    for (let j = 0; j < n - 1 - i; j++) {
      if (arr[j] > arr[j + 1]) {
        // Swap
        [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]];
      }
    }
  }

  return arr;
}

console.log(bubbleSort([5, 3, 1, 4, 2]));  // [1, 2, 3, 4, 5]
```

**Optimization:** If a full pass makes no swaps, the array is already sorted — stop early.

```javascript
function bubbleSortOptimized(arr) {
  const n = arr.length;

  for (let i = 0; i < n - 1; i++) {
    let swapped = false;

    for (let j = 0; j < n - 1 - i; j++) {
      if (arr[j] > arr[j + 1]) {
        [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]];
        swapped = true;
      }
    }

    if (!swapped) break;  // already sorted!
  }

  return arr;
}
```

- Time: O(n²) worst/average case, O(n) best case (already sorted)
- Space: O(1) — in-place

---

## Selection Sort

**Idea:** Find the minimum element in the unsorted portion, then swap it to the front. After each pass, one more element is in its final sorted position.

```
arr = [5, 3, 1, 4, 2]

Pass 1: find min in [5,3,1,4,2] → 1 at index 2. Swap with index 0.
→ [1, 3, 5, 4, 2]

Pass 2: find min in [3,5,4,2] → 2 at index 4. Swap with index 1.
→ [1, 2, 5, 4, 3]

Pass 3: find min in [5,4,3] → 3 at index 4. Swap with index 2.
→ [1, 2, 3, 4, 5]

Pass 4: find min in [4,5] → 4 at index 3. Swap with itself.
→ [1, 2, 3, 4, 5]

Done ✓
```

```javascript
function selectionSort(arr) {
  const n = arr.length;

  for (let i = 0; i < n - 1; i++) {
    // Find the minimum in the unsorted portion [i..n-1]
    let minIndex = i;
    for (let j = i + 1; j < n; j++) {
      if (arr[j] < arr[minIndex]) {
        minIndex = j;
      }
    }

    // Swap minimum to its correct position
    if (minIndex !== i) {
      [arr[i], arr[minIndex]] = [arr[minIndex], arr[i]];
    }
  }

  return arr;
}

console.log(selectionSort([5, 3, 1, 4, 2]));  // [1, 2, 3, 4, 5]
```

- Time: O(n²) always (always scans the rest of the array)
- Space: O(1) — in-place

---

## Comparing Bubble and Selection Sort

| | Bubble Sort | Selection Sort |
|--|-------------|----------------|
| Time | O(n²) worst, O(n) best | O(n²) always |
| Space | O(1) | O(1) |
| Swaps | Many (up to n²) | At most n |
| Stable? | Yes (equal elements stay in order) | No |
| Adaptive? | Yes (stops early if sorted) | No |

**Stable sort:** equal elements stay in their original relative order.
**Adaptive sort:** faster when input is already partially sorted.

---

## Insertion Sort (Bonus)

**Idea:** Build a sorted portion one element at a time. Like sorting playing cards in your hand — pick each card and insert it into the right position among the cards you've already sorted.

```
arr = [5, 3, 1, 4, 2]

Start: sorted part = [5]

Pick 3: 3 < 5, insert before 5 → [3, 5]
Pick 1: 1 < 5, 1 < 3, insert at front → [1, 3, 5]
Pick 4: 4 < 5, 4 > 3, insert between → [1, 3, 4, 5]
Pick 2: 2 < 4, 2 < 3, 2 > 1, insert → [1, 2, 3, 4, 5]
```

```javascript
function insertionSort(arr) {
  for (let i = 1; i < arr.length; i++) {
    const key = arr[i];   // the element to insert
    let j = i - 1;

    // Shift elements that are greater than key one position to the right
    while (j >= 0 && arr[j] > key) {
      arr[j + 1] = arr[j];
      j--;
    }

    arr[j + 1] = key;  // insert key in correct position
  }

  return arr;
}

console.log(insertionSort([5, 3, 1, 4, 2]));  // [1, 2, 3, 4, 5]
```

- Time: O(n²) worst, O(n) best (already sorted — no inner loop work needed)
- Space: O(1)

**Insertion sort is actually better than bubble/selection for small arrays and nearly-sorted arrays.** In practice, many hybrid sorting algorithms (like JavaScript's internal sort) use insertion sort for small subarrays.

---

## Summary

| Algorithm | Best | Average | Worst | Stable | When to Use |
|-----------|------|---------|-------|--------|-------------|
| Bubble Sort | O(n) | O(n²) | O(n²) | Yes | Teaching, nearly sorted |
| Selection Sort | O(n²) | O(n²) | O(n²) | No | When swaps are expensive |
| Insertion Sort | O(n) | O(n²) | O(n²) | Yes | Small arrays, nearly sorted |

All three are O(n²). The next days cover O(n log n) algorithms which are much faster.

---

## Practice Problem

Write bubble sort but in DESCENDING order (largest first).
```
Input: [5, 3, 1, 4, 2]
Output: [5, 4, 3, 2, 1]
```

## Answer

```javascript
function bubbleSortDesc(arr) {
  const n = arr.length;
  for (let i = 0; i < n - 1; i++) {
    for (let j = 0; j < n - 1 - i; j++) {
      if (arr[j] < arr[j + 1]) {  // only change: < instead of >
        [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]];
      }
    }
  }
  return arr;
}
```

---

**Next: Day 30 — Merge Sort**
The first O(n log n) algorithm. Uses the "divide and conquer" strategy — split the array in half, sort each half, merge them back together.
