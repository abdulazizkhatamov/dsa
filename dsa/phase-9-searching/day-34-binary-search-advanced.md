# Day 34 — Binary Search: Advanced Applications

---

## Binary Search on a 2D Matrix

**Problem:** Given a matrix where rows are sorted left-to-right, and the first element of each row is greater than the last element of the previous row — search for a target.

```
Matrix:
[[ 1,  3,  5,  7],
 [10, 11, 16, 20],
 [23, 30, 34, 60]]

target = 3 → true
target = 13 → false
```

**Key insight:** Treat the 2D matrix as a 1D sorted array. Element at 1D position `k` corresponds to `matrix[Math.floor(k/cols)][k % cols]`.

```javascript
function searchMatrix(matrix, target) {
  const rows = matrix.length;
  const cols = matrix[0].length;

  let left = 0;
  let right = rows * cols - 1;

  while (left <= right) {
    const mid = Math.floor((left + right) / 2);
    const row = Math.floor(mid / cols);
    const col = mid % cols;
    const val = matrix[row][col];

    if (val === target) return true;
    if (val < target) left = mid + 1;
    else right = mid - 1;
  }

  return false;
}

const matrix = [[1,3,5,7],[10,11,16,20],[23,30,34,60]];
console.log(searchMatrix(matrix, 3));   // true
console.log(searchMatrix(matrix, 13));  // false
```

---

## Binary Search on Answer Space

**Template:** When the answer is a number and you can check "is this value too small/too big?", use binary search on the range of possible answers.

```javascript
function binarySearchOnAnswer(lo, hi, condition) {
  // condition(mid) returns true if mid is valid, false if invalid
  // Find the smallest valid answer
  while (lo < hi) {
    const mid = Math.floor((lo + hi) / 2);
    if (condition(mid)) {
      hi = mid;      // mid works, but maybe smaller also works
    } else {
      lo = mid + 1;  // mid doesn't work, need bigger
    }
  }
  return lo;
}
```

---

## Problem: Koko Eating Bananas

**Problem:** Koko has piles of bananas. She can eat `k` bananas per hour. Given `h` hours, find the minimum `k` such that she can eat all bananas in time.

**Input:** `piles = [3, 6, 7, 11]`, `h = 8`
**Output:** `4` (with speed 4: ceil(3/4)+ceil(6/4)+ceil(7/4)+ceil(11/4) = 1+2+2+3 = 8 hours)

**Answer space:** k is between 1 and max(piles).
**Condition:** can Koko finish all piles at speed k in h hours?

```javascript
function minEatingSpeed(piles, h) {
  function canFinish(speed) {
    let hours = 0;
    for (const pile of piles) {
      hours += Math.ceil(pile / speed);
    }
    return hours <= h;
  }

  let left = 1;
  let right = Math.max(...piles);

  while (left < right) {
    const mid = Math.floor((left + right) / 2);
    if (canFinish(mid)) {
      right = mid;      // valid speed, try smaller
    } else {
      left = mid + 1;   // too slow, need faster
    }
  }

  return left;
}

console.log(minEatingSpeed([3, 6, 7, 11], 8));   // 4
console.log(minEatingSpeed([30, 11, 23, 4, 20], 5));  // 30
console.log(minEatingSpeed([30, 11, 23, 4, 20], 6));  // 23
```

---

## Problem: Split Array Largest Sum

**Problem:** Split an array into `m` non-empty parts. Minimize the maximum sum of any part.

**Input:** `nums = [7,2,5,10,8]`, `m = 2`
**Output:** `18` (split into [7,2,5] and [10,8] — max sums are 14 and 18)

**Answer space:** The answer is between max(nums) and sum(nums).
- minimum: at least one element per subarray → max element is the floor
- maximum: one subarray with everything → sum of all elements

```javascript
function splitArray(nums, m) {
  function canSplit(maxSum) {
    let parts = 1;
    let currentSum = 0;

    for (const num of nums) {
      if (currentSum + num > maxSum) {
        parts++;           // start a new part
        currentSum = num;
        if (parts > m) return false;  // too many parts needed
      } else {
        currentSum += num;
      }
    }

    return true;
  }

  let left = Math.max(...nums);
  let right = nums.reduce((sum, n) => sum + n, 0);

  while (left < right) {
    const mid = Math.floor((left + right) / 2);
    if (canSplit(mid)) {
      right = mid;      // valid, try smaller max
    } else {
      left = mid + 1;   // invalid, need larger max
    }
  }

  return left;
}

console.log(splitArray([7, 2, 5, 10, 8], 2));  // 18
console.log(splitArray([1, 2, 3, 4, 5], 2));   // 9
```

---

## Problem: Median of Two Sorted Arrays

**Problem:** Find the median of two sorted arrays combined. Do it in O(log(n+m)).

**Input:** `nums1 = [1,3]`, `nums2 = [2]`
**Output:** `2.0`

**Input:** `nums1 = [1,2]`, `nums2 = [3,4]`
**Output:** `2.5`

This is a hard problem but is famous in interviews (LeetCode #4).

**Key insight:** Binary search on the partition of the smaller array. Find where to split both arrays so that the left side has exactly half of all elements.

```javascript
function findMedianSortedArrays(nums1, nums2) {
  // Ensure nums1 is the shorter array
  if (nums1.length > nums2.length) {
    return findMedianSortedArrays(nums2, nums1);
  }

  const m = nums1.length;
  const n = nums2.length;
  const halfLen = Math.floor((m + n + 1) / 2);

  let left = 0, right = m;

  while (left <= right) {
    const i = Math.floor((left + right) / 2);  // partition in nums1
    const j = halfLen - i;                       // partition in nums2

    const maxLeft1 = i === 0 ? -Infinity : nums1[i - 1];
    const minRight1 = i === m ? Infinity : nums1[i];
    const maxLeft2 = j === 0 ? -Infinity : nums2[j - 1];
    const minRight2 = j === n ? Infinity : nums2[j];

    if (maxLeft1 <= minRight2 && maxLeft2 <= minRight1) {
      // Found correct partition
      if ((m + n) % 2 === 1) {
        return Math.max(maxLeft1, maxLeft2);
      } else {
        return (Math.max(maxLeft1, maxLeft2) + Math.min(minRight1, minRight2)) / 2;
      }
    } else if (maxLeft1 > minRight2) {
      right = i - 1;  // move left in nums1
    } else {
      left = i + 1;   // move right in nums1
    }
  }
}

console.log(findMedianSortedArrays([1, 3], [2]));     // 2.0
console.log(findMedianSortedArrays([1, 2], [3, 4]));  // 2.5
```

---

## Phase 9 Self-Check

- [ ] What are the requirements for binary search to work?
- [ ] Write the binary search template from memory
- [ ] What is the loop condition? What are the updates for left and right?
- [ ] How do you find the first occurrence in a sorted array with duplicates?
- [ ] What is "binary search on answer space"?
- [ ] How do you treat a 2D matrix as 1D for binary search?

---

**Next: Day 35 — Searching: Review + Linear Search and Hash-Based Search**
We'll round out Phase 9 with linear search (when binary search isn't an option), hash-based lookups, and a review of all searching techniques.
