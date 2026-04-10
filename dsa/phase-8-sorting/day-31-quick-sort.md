# Day 31 — Quick Sort

---

## The Idea

Quick Sort also uses **divide and conquer**, but differently from merge sort.

Instead of splitting exactly in half, Quick Sort picks a **pivot** element and **partitions** the array:
- All elements **smaller** than pivot go to the LEFT
- All elements **larger** than pivot go to the RIGHT
- The pivot is now in its FINAL sorted position

Then recursively sort the left and right portions.

```
arr = [3, 6, 8, 10, 1, 2, 1]

Pick pivot = 3 (first element for simplicity)

Partition:
[1, 2, 1]  [3]  [6, 8, 10]
  < 3      pivot   > 3

Recursively sort left:  [1, 1, 2]
Recursively sort right: [6, 8, 10]

Result: [1, 1, 2, 3, 6, 8, 10] ✓
```

---

## The Partition Step

This is the heart of Quick Sort. It rearranges the array in-place so that:
- Elements less than pivot are on the left
- Elements greater than pivot are on the right
- Returns the pivot's final index

```
arr = [3, 6, 8, 10, 1, 2, 1], pivot = 3 (index 0)

We'll move pivot to the end temporarily, then use two pointers:

arr = [6, 8, 10, 1, 2, 1, 3]  (pivot moved to end)

i = -1  (marks where the "less than" zone ends)
j scans from left to right

j=0: 6 > 3, skip
j=1: 8 > 3, skip
j=2: 10 > 3, skip
j=3: 1 ≤ 3! i++ → i=0. Swap arr[0] and arr[3]. arr=[1,8,10,6,2,1,3]
j=4: 2 ≤ 3! i++ → i=1. Swap arr[1] and arr[4]. arr=[1,2,10,6,8,1,3]
j=5: 1 ≤ 3! i++ → i=2. Swap arr[2] and arr[5]. arr=[1,2,1,6,8,10,3]

Put pivot back at i+1 = index 3:
arr=[1,2,1,3,8,10,6]

Pivot (3) is at index 3 — its final position ✓
Left: [1,2,1], Right: [8,10,6]
```

```javascript
function partition(arr, low, high) {
  const pivot = arr[high];  // use last element as pivot
  let i = low - 1;          // pointer for smaller elements

  for (let j = low; j < high; j++) {
    if (arr[j] <= pivot) {
      i++;
      [arr[i], arr[j]] = [arr[j], arr[i]];  // swap
    }
  }

  [arr[i + 1], arr[high]] = [arr[high], arr[i + 1]];  // place pivot
  return i + 1;  // pivot's final index
}
```

---

## Full Quick Sort

```javascript
function quickSort(arr, low = 0, high = arr.length - 1) {
  if (low >= high) return;  // base case: 0 or 1 element, already sorted

  const pivotIndex = partition(arr, low, high);

  // Recursively sort left of pivot
  quickSort(arr, low, pivotIndex - 1);

  // Recursively sort right of pivot
  quickSort(arr, pivotIndex + 1, high);
}

function partition(arr, low, high) {
  const pivot = arr[high];
  let i = low - 1;

  for (let j = low; j < high; j++) {
    if (arr[j] <= pivot) {
      i++;
      [arr[i], arr[j]] = [arr[j], arr[i]];
    }
  }

  [arr[i + 1], arr[high]] = [arr[high], arr[i + 1]];
  return i + 1;
}

const arr = [5, 3, 1, 4, 2];
quickSort(arr);
console.log(arr);  // [1, 2, 3, 4, 5]

const arr2 = [3, 6, 8, 10, 1, 2, 1];
quickSort(arr2);
console.log(arr2);  // [1, 1, 2, 3, 6, 8, 10]
```

Note: Quick Sort sorts **in-place** — it modifies the original array.

---

## Why O(n log n) Average, O(n²) Worst?

**Best/Average case:** The pivot splits the array roughly in half each time.
- log n levels of recursion
- O(n) work per level
- Total: O(n log n)

**Worst case:** The pivot is always the smallest or largest element (e.g., already sorted array with first/last element as pivot).
- One side has 0 elements, other has n-1 elements
- Not dividing in half → n levels instead of log n
- Total: O(n²)

**Fixing the worst case:** Use a **random pivot** instead of always picking the last element.

```javascript
function partition(arr, low, high) {
  // Pick random pivot and swap to end
  const randomIndex = Math.floor(Math.random() * (high - low + 1)) + low;
  [arr[randomIndex], arr[high]] = [arr[high], arr[randomIndex]];

  const pivot = arr[high];
  let i = low - 1;

  for (let j = low; j < high; j++) {
    if (arr[j] <= pivot) {
      i++;
      [arr[i], arr[j]] = [arr[j], arr[i]];
    }
  }

  [arr[i + 1], arr[high]] = [arr[high], arr[i + 1]];
  return i + 1;
}
```

With random pivot, worst case is extremely unlikely in practice.

---

## Quick Sort vs Merge Sort

| | Quick Sort | Merge Sort |
|--|-----------|------------|
| Average time | O(n log n) | O(n log n) |
| Worst time | O(n²) | O(n log n) |
| Space | O(log n) call stack | O(n) extra arrays |
| In-place? | Yes | No |
| Stable? | No | Yes |
| Cache efficiency | Better (in-place) | Worse (new arrays) |

**In practice:** Quick Sort is often faster than Merge Sort despite the same Big O, because it has better cache performance (in-place = better memory locality). Most standard library sorts use Quick Sort or a variant.

---

## Bonus: Quick Select (Find the Kth Largest)

**Problem:** Find the k-th largest element in an unsorted array.

**Naive:** Sort the array → O(n log n), then return `arr[n-k]`.

**Better with Quick Select:** O(n) average — same partition idea, but only recurse into ONE side.

```javascript
function findKthLargest(nums, k) {
  // k-th largest = (n-k)-th smallest (0-indexed)
  const target = nums.length - k;

  function quickSelect(low, high) {
    if (low === high) return nums[low];

    const pivotIndex = partition(nums, low, high);

    if (pivotIndex === target) return nums[pivotIndex];
    if (pivotIndex < target) return quickSelect(pivotIndex + 1, high);
    return quickSelect(low, pivotIndex - 1);
  }

  return quickSelect(0, nums.length - 1);
}

console.log(findKthLargest([3, 2, 1, 5, 6, 4], 2));  // 5  (2nd largest)
console.log(findKthLargest([3, 2, 3, 1, 2, 4, 5, 5, 6], 4));  // 4
```

- Average: O(n) — each time we recurse into only one half (average n/2 elements)
- Worst: O(n²) with bad pivot choices

---

## Summary

| Algorithm | Time (avg) | Time (worst) | Space | In-place | Stable |
|-----------|-----------|--------------|-------|----------|--------|
| Bubble Sort | O(n²) | O(n²) | O(1) | Yes | Yes |
| Selection Sort | O(n²) | O(n²) | O(1) | Yes | No |
| Insertion Sort | O(n²) | O(n²) | O(1) | Yes | Yes |
| Merge Sort | O(n log n) | O(n log n) | O(n) | No | Yes |
| Quick Sort | O(n log n) | O(n²) | O(log n) | Yes | No |

---

**Next: Day 32 — Sorting: Review and When to Use Each**
We'll compare all sorting algorithms, look at JavaScript's built-in sort, and solve sorting-related interview problems.
