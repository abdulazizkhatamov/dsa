# Day 32 — Sorting: Review and When to Use Each

---

## JavaScript's Built-In Sort

```javascript
// Default sort (DO NOT use for numbers — sorts alphabetically!)
[10, 9, 2, 21, 3].sort()
// [10, 2, 21, 3, 9]  ← WRONG

// Correct: provide a comparator function
[10, 9, 2, 21, 3].sort((a, b) => a - b)
// [2, 3, 9, 10, 21]  ← ascending

[10, 9, 2, 21, 3].sort((a, b) => b - a)
// [21, 10, 9, 3, 2]  ← descending

// Sorting objects
const people = [{name:"Ali",age:30},{name:"Sara",age:25},{name:"John",age:35}];
people.sort((a, b) => a.age - b.age);
// sorted by age ascending
```

**The comparator rule:**
- Returns negative → `a` comes before `b`
- Returns positive → `b` comes before `a`
- Returns 0 → keep original order

JavaScript's `Array.sort()` uses **TimSort** internally — a hybrid of Merge Sort and Insertion Sort. It's O(n log n) and stable.

---

## Problem 1: Sort Colors (Dutch National Flag)

**Problem:** Given an array containing only 0, 1, and 2, sort it in-place in O(n) time and O(1) space. (Can't use `sort()`.)

**Input:** `[2, 0, 2, 1, 1, 0]`
**Output:** `[0, 0, 1, 1, 2, 2]`

**Three-pointer approach:**
- `low`: everything before low is 0
- `mid`: current element being examined
- `high`: everything after high is 2
- Between low and high: 1s (or unknown)

```javascript
function sortColors(arr) {
  let low = 0;
  let mid = 0;
  let high = arr.length - 1;

  while (mid <= high) {
    if (arr[mid] === 0) {
      [arr[low], arr[mid]] = [arr[mid], arr[low]];
      low++;
      mid++;
    } else if (arr[mid] === 1) {
      mid++;  // 1 is in the right place
    } else {  // arr[mid] === 2
      [arr[mid], arr[high]] = [arr[high], arr[mid]];
      high--;
      // Don't increment mid — new arr[mid] from the swap needs to be examined
    }
  }

  return arr;
}

console.log(sortColors([2, 0, 2, 1, 1, 0]));  // [0, 0, 1, 1, 2, 2]
console.log(sortColors([2, 0, 1]));            // [0, 1, 2]
```

**Trace:**
```
[2, 0, 2, 1, 1, 0], low=0, mid=0, high=5

arr[0]=2: swap mid(0) with high(5) → [0, 0, 2, 1, 1, 2], high=4
arr[0]=0: swap low(0) with mid(0) → [0, 0, 2, 1, 1, 2], low=1, mid=1
arr[1]=0: swap low(1) with mid(1) → no change, low=2, mid=2
arr[2]=2: swap mid(2) with high(4) → [0, 0, 1, 1, 2, 2], high=3
arr[2]=1: mid=3
arr[3]=1: mid=4
mid(4) > high(3) → stop

Result: [0, 0, 1, 1, 2, 2] ✓
```

---

## Problem 2: Merge Intervals

**Problem:** Given a list of intervals, merge all overlapping intervals.

**Input:** `[[1,3],[2,6],[8,10],[15,18]]`
**Output:** `[[1,6],[8,10],[15,18]]`

Explanation: [1,3] and [2,6] overlap → merge to [1,6].

```javascript
function mergeIntervals(intervals) {
  if (intervals.length <= 1) return intervals;

  // Sort by start time
  intervals.sort((a, b) => a[0] - b[0]);

  const result = [intervals[0]];

  for (let i = 1; i < intervals.length; i++) {
    const current = intervals[i];
    const lastMerged = result[result.length - 1];

    if (current[0] <= lastMerged[1]) {
      // Overlapping — extend the last interval if needed
      lastMerged[1] = Math.max(lastMerged[1], current[1]);
    } else {
      // No overlap — add as new interval
      result.push(current);
    }
  }

  return result;
}

console.log(mergeIntervals([[1,3],[2,6],[8,10],[15,18]]));
// [[1,6],[8,10],[15,18]]

console.log(mergeIntervals([[1,4],[4,5]]));
// [[1,5]]  (they touch at 4)

console.log(mergeIntervals([[1,4],[0,4]]));
// [[0,4]]  (after sorting)
```

- Time: O(n log n) for sorting, O(n) for merging = O(n log n) total
- Space: O(n) for result

---

## Problem 3: Largest Number

**Problem:** Given a list of non-negative integers, arrange them so the formed number is the largest possible.

**Input:** `[3, 30, 34, 5, 9]`
**Output:** `"9534330"` (9, 5, 34, 3, 30)

**Key insight:** Instead of comparing numbers directly, compare by which combination is larger: does `"34"+"3"` = "343" > "334" = `"3"+"34"`? Yes, so 34 comes before 3.

```javascript
function largestNumber(nums) {
  const sorted = nums
    .map(String)           // convert to strings
    .sort((a, b) => (b + a) > (a + b) ? 1 : -1);  // compare concatenations

  // Edge case: all zeros
  if (sorted[0] === "0") return "0";

  return sorted.join("");
}

console.log(largestNumber([3, 30, 34, 5, 9]));  // "9534330"
console.log(largestNumber([10, 2]));             // "210"
console.log(largestNumber([0, 0]));              // "0"
```

---

## When to Use Each Sorting Algorithm

| Situation | Algorithm | Why |
|-----------|-----------|-----|
| General purpose, production | JavaScript's `arr.sort()` | It's TimSort — fast, stable, optimized |
| Interview: sort array | Quick Sort or Merge Sort | Show you understand O(n log n) |
| Need stable sort | Merge Sort | Quick Sort is not stable |
| Need in-place, don't care about stability | Quick Sort | Better cache performance |
| Nearly sorted data | Insertion Sort | O(n) on sorted input |
| Very small array (< 20 items) | Insertion Sort | Overhead of merge/quick is not worth it |
| Sort linked list | Merge Sort | Natural for linked lists (no random access needed) |
| Find k-th largest | Quick Select | O(n) average, no full sort needed |

---

## Phase 8 Self-Check

- [ ] What is the Big O of bubble, selection, insertion sort?
- [ ] What is "divide and conquer"?
- [ ] How does merge sort work? What is the merge step?
- [ ] Why is merge sort O(n log n)?
- [ ] How does quick sort work? What is a pivot?
- [ ] When is quick sort O(n²)?
- [ ] What is TimSort?
- [ ] How do you sort an array of numbers in JavaScript correctly?
- [ ] What is Quick Select? When do you use it?

---

**Next: Day 33 — Binary Search**
Phase 9 begins. Binary search is O(log n) — it finds a value in a sorted array in at most 30 steps, no matter if the array has 1 billion elements. One of the most fundamental algorithms.
