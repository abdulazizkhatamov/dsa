# Day 48 — Advanced Patterns: Two Pointers and Sliding Window

---

## You Know the Basics — Now Go Deeper

You've already learned Two Pointers (Day 10) and Sliding Window (Day 11). This day focuses on harder problems that require you to combine these techniques or apply them in non-obvious ways.

---

## Advanced Two Pointers: Three Sum

**Problem:** Find all unique triplets in an array that sum to zero.

**Input:** `[-1, 0, 1, 2, -1, -4]`
**Output:** `[[-1,-1,2],[-1,0,1]]`

**Approach:** Sort the array. For each element, use two pointers to find pairs that sum to its negative.

```javascript
function threeSum(nums) {
  nums.sort((a, b) => a - b);
  const result = [];

  for (let i = 0; i < nums.length - 2; i++) {
    // Skip duplicates for the first element
    if (i > 0 && nums[i] === nums[i-1]) continue;

    let left = i + 1;
    let right = nums.length - 1;

    while (left < right) {
      const sum = nums[i] + nums[left] + nums[right];

      if (sum === 0) {
        result.push([nums[i], nums[left], nums[right]]);

        // Skip duplicates
        while (left < right && nums[left] === nums[left+1]) left++;
        while (left < right && nums[right] === nums[right-1]) right--;

        left++;
        right--;
      } else if (sum < 0) {
        left++;
      } else {
        right--;
      }
    }
  }

  return result;
}

console.log(threeSum([-1, 0, 1, 2, -1, -4]));  // [[-1,-1,2],[-1,0,1]]
console.log(threeSum([0, 0, 0]));               // [[0,0,0]]
```

- Time: O(n²) — O(n log n) sort + O(n) outer × O(n) inner = O(n²)
- Space: O(1) excluding output

---

## Advanced Two Pointers: Trapping Rain Water

**Problem:** Given heights, calculate how much water is trapped between bars after rain.

```
Input: [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6

Visual:
     #
  #  ##  #
  ## ###~##~#
  ############

~ = trapped water
```

**Key insight:** Water at position i is trapped to the height of `min(maxLeft, maxRight) - height[i]`.

**Two-pointer approach (O(1) space):**

```javascript
function trap(height) {
  let left = 0, right = height.length - 1;
  let maxLeft = 0, maxRight = 0;
  let water = 0;

  while (left < right) {
    if (height[left] <= height[right]) {
      // Left side is the limiting factor
      if (height[left] >= maxLeft) {
        maxLeft = height[left];  // update max
      } else {
        water += maxLeft - height[left];  // trapped water
      }
      left++;
    } else {
      // Right side is the limiting factor
      if (height[right] >= maxRight) {
        maxRight = height[right];
      } else {
        water += maxRight - height[right];
      }
      right--;
    }
  }

  return water;
}

console.log(trap([0,1,0,2,1,0,1,3,2,1,2,1]));  // 6
console.log(trap([4,2,0,3,2,5]));               // 9
```

---

## Advanced Sliding Window: Fruit Into Baskets

**Problem:** You have two baskets. Each can hold only one type of fruit. Going along a row of trees, pick as many fruits as possible. You can start from any tree but must stop when you'd need a 3rd type.

This is exactly "Longest Subarray with at most 2 distinct values" — which you solved in Day 16!

```javascript
// Using the map-based sliding window from Day 16:
function totalFruit(fruits) {
  return longestWithKDistinct(fruits, 2);
}
```

The lesson: many problems are restatements of patterns you already know.

---

## Advanced Sliding Window: Max Consecutive Ones III

**Problem:** Given a binary array (0s and 1s) and integer k, find the max length of 1s you can get by flipping at most k zeros.

**Input:** `[1,1,1,0,0,0,1,1,1,1,0]`, `k=2`
**Output:** `6` (flip the two 0s at index 3 and 10 → `[1,1,1,1,1,1,1,1,1,1,0]`, wait that's not right — flip index 9 and 10: `[1,1,1,0,0,0,1,1,1,1,1,1]`, 6 consecutive from index 6)

Actually: flip index 4 and 10 → `[1,1,1,0,1,0,1,1,1,1,1,0]`... let me think: the answer is the longest window with at most k zeros.

```javascript
function longestOnes(nums, k) {
  let left = 0;
  let zerosInWindow = 0;
  let maxLength = 0;

  for (let right = 0; right < nums.length; right++) {
    if (nums[right] === 0) zerosInWindow++;

    // If too many zeros, shrink from left
    while (zerosInWindow > k) {
      if (nums[left] === 0) zerosInWindow--;
      left++;
    }

    maxLength = Math.max(maxLength, right - left + 1);
  }

  return maxLength;
}

console.log(longestOnes([1,1,1,0,0,0,1,1,1,1,0], 2));  // 6
console.log(longestOnes([0,0,1,1,0,0,1,1,1,0,1,1,0,0,0,1,1,1,1], 3));  // 10
```

---

## The Meta-Pattern: Recognize Before You Code

The best DSA practitioners don't memorize solutions. They recognize the **pattern** and apply the template.

| If the problem says... | Think... |
|----------------------|---------|
| "Find pair/triplet that sums to X" | Two pointers (sort first) |
| "Longest subarray with property X" | Sliding window (variable) |
| "Every subarray of size k" | Sliding window (fixed) |
| "Find all combinations/subsets" | Backtracking |
| "Minimum/maximum in all windows" | Monotonic deque |
| "Trap water / largest rectangle" | Monotonic stack |
| "Count subarrays with sum k" | Prefix sum + hashmap |
| "Any path exists" | DFS + visited |
| "Shortest path" | BFS |
| "Optimal choice at each step" | DP or Greedy |

---

**Next: Day 49 — Advanced Patterns: Backtracking and Greedy**
Harder backtracking problems and introduction to Greedy algorithms — where you make the locally optimal choice at each step.
