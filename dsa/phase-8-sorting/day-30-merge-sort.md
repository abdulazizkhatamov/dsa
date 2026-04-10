# Day 30 — Merge Sort

---

## Why Is It Faster?

Bubble, selection, and insertion sort are all O(n²). For 1,000 elements: 1,000,000 operations.

**Merge Sort is O(n log n).** For 1,000 elements: ~10,000 operations. For 1,000,000 elements: ~20,000,000 vs 1,000,000,000,000. Massive difference.

The key is **Divide and Conquer**: instead of one big hard problem, break it into two smaller problems, solve each, and combine.

---

## The Idea

1. **Divide:** Split the array in half
2. **Conquer:** Recursively sort each half
3. **Combine:** Merge the two sorted halves into one sorted array

```
[5, 3, 1, 4, 2]

Divide:
[5, 3]     [1, 4, 2]

Divide again:
[5] [3]    [1]  [4, 2]

Divide again:
[5] [3]    [1]  [4] [2]

Now merge:
[3, 5]     [1]  [2, 4]

Merge again:
[1, 3, 5]     [2, 4]

Merge final:
[1, 2, 3, 4, 5] ✓
```

---

## The Merge Function

Before writing merge sort, let's write the `merge` function — it merges two already-sorted arrays into one sorted array.

```
Input: [1, 3, 5]  and  [2, 4, 6]
Output: [1, 2, 3, 4, 5, 6]

Use two pointers:
i=0, j=0
Compare 1 vs 2: 1 < 2, take 1. result=[1], i=1
Compare 3 vs 2: 3 > 2, take 2. result=[1,2], j=1
Compare 3 vs 4: 3 < 4, take 3. result=[1,2,3], i=2
Compare 5 vs 4: 5 > 4, take 4. result=[1,2,3,4], j=2
Compare 5 vs 6: 5 < 6, take 5. result=[1,2,3,4,5], i=3 (end of left)
Take remaining from right: result=[1,2,3,4,5,6] ✓
```

```javascript
function merge(left, right) {
  const result = [];
  let i = 0;
  let j = 0;

  while (i < left.length && j < right.length) {
    if (left[i] <= right[j]) {
      result.push(left[i]);
      i++;
    } else {
      result.push(right[j]);
      j++;
    }
  }

  // Add any remaining elements (one of the arrays may have leftovers)
  while (i < left.length) {
    result.push(left[i]);
    i++;
  }
  while (j < right.length) {
    result.push(right[j]);
    j++;
  }

  return result;
}

console.log(merge([1, 3, 5], [2, 4, 6]));  // [1, 2, 3, 4, 5, 6]
console.log(merge([1], [2]));               // [1, 2]
console.log(merge([3], [1, 2]));            // [1, 2, 3]
```

---

## Full Merge Sort

```javascript
function mergeSort(arr) {
  // Base case: array of 0 or 1 element is already sorted
  if (arr.length <= 1) return arr;

  // Divide: split in half
  const mid = Math.floor(arr.length / 2);
  const left = arr.slice(0, mid);
  const right = arr.slice(mid);

  // Conquer: recursively sort each half
  const sortedLeft = mergeSort(left);
  const sortedRight = mergeSort(right);

  // Combine: merge the sorted halves
  return merge(sortedLeft, sortedRight);
}

console.log(mergeSort([5, 3, 1, 4, 2]));        // [1, 2, 3, 4, 5]
console.log(mergeSort([38, 27, 43, 3, 9, 82])); // [3, 9, 27, 38, 43, 82]
console.log(mergeSort([1]));                     // [1]
console.log(mergeSort([]));                      // []
```

---

## Why O(n log n)?

**The division:** Each time we split an array in half. Starting from n elements, we split log₂(n) times before getting to single elements. So there are **log n levels**.

**Each level:** At every level of the recursion, we process ALL n elements total (just in different-sized groups).

```
Level 0: 1 array of n elements              = n total elements
Level 1: 2 arrays of n/2 elements           = n total elements
Level 2: 4 arrays of n/4 elements           = n total elements
...
Level log n: n arrays of 1 element          = n total elements
```

Merging work per level = O(n). Levels = O(log n). Total = **O(n log n)**.

---

## Call Tree for [5, 3, 1, 4, 2]

```
mergeSort([5,3,1,4,2])
├── mergeSort([5,3])
│   ├── mergeSort([5]) → [5]
│   ├── mergeSort([3]) → [3]
│   └── merge([5],[3]) → [3,5]
├── mergeSort([1,4,2])
│   ├── mergeSort([1]) → [1]
│   ├── mergeSort([4,2])
│   │   ├── mergeSort([4]) → [4]
│   │   ├── mergeSort([2]) → [2]
│   │   └── merge([4],[2]) → [2,4]
│   └── merge([1],[2,4]) → [1,2,4]
└── merge([3,5],[1,2,4]) → [1,2,3,4,5]
```

---

## Properties of Merge Sort

- **Time:** O(n log n) — always (best, average, and worst case)
- **Space:** O(n) — needs extra arrays for merging (not in-place)
- **Stable:** Yes — equal elements maintain their relative order

Merge Sort is the algorithm of choice when:
- You need a guaranteed O(n log n) (quick sort can be O(n²) in worst case)
- You need a stable sort
- You're sorting linked lists (merge sort is natural for linked lists)

---

## Bonus: Counting Inversions

A classic interview problem that uses merge sort.

**Problem:** Count how many pairs (i, j) exist where i < j but arr[i] > arr[j] (i.e., the element is "out of order").

**Input:** `[3, 1, 2]`
**Output:** `2` (pairs: (3,1) and (3,2))

The idea: during the merge step, whenever you take an element from the RIGHT array before exhausting the LEFT array, all remaining elements in the LEFT array form inversions with it.

```javascript
function countInversions(arr) {
  let count = 0;

  function mergeSortCount(arr) {
    if (arr.length <= 1) return arr;

    const mid = Math.floor(arr.length / 2);
    const left = mergeSortCount(arr.slice(0, mid));
    const right = mergeSortCount(arr.slice(mid));

    return mergeCount(left, right);
  }

  function mergeCount(left, right) {
    const result = [];
    let i = 0, j = 0;

    while (i < left.length && j < right.length) {
      if (left[i] <= right[j]) {
        result.push(left[i++]);
      } else {
        // All remaining elements in left are > right[j]
        count += left.length - i;  // each forms an inversion
        result.push(right[j++]);
      }
    }

    return result.concat(left.slice(i)).concat(right.slice(j));
  }

  mergeSortCount(arr);
  return count;
}

console.log(countInversions([3, 1, 2]));  // 2
console.log(countInversions([1, 2, 3]));  // 0 (already sorted)
console.log(countInversions([3, 2, 1]));  // 3 (fully reversed)
```

---

## Summary

| Step | What Happens |
|------|-------------|
| Divide | Split array in half (recursively) until single elements |
| Conquer | Single elements are already "sorted" |
| Combine | Merge pairs of sorted arrays using two pointers |

**Merge Sort is O(n log n) because:** log n levels × O(n) work per level.

---

**Next: Day 31 — Quick Sort**
Another O(n log n) algorithm, but uses a different strategy: instead of always splitting in half, it picks a "pivot" and partitions the array around it.
