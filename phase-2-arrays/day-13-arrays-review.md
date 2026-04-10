# Day 13 — Arrays: Review and Mixed Practice

---

## What You've Learned in Phase 2

Before moving on, let's recap every technique from the array phase:

| Day | Technique | What it Does | Time |
|-----|-----------|--------------|------|
| Day 8 | Array basics | Access, add, remove | O(1) or O(n) |
| Day 9 | Basic problems | Max, reverse, duplicates | O(n) |
| Day 10 | Two Pointers | Compare/move two indexes | O(n) |
| Day 11 | Sliding Window | Track a range in array | O(n) |
| Day 12 | Prefix Sum | Pre-compute sums for range queries | O(1) per query |

---

## How to Know Which Technique to Use

When you see an array problem, ask these questions in order:

**1. Do you need the sum of a range repeatedly?**
→ Prefix Sum

**2. Do you need the longest/shortest subarray meeting a condition?**
→ Sliding Window (variable size)

**3. Do you need sum/average of every window of size k?**
→ Sliding Window (fixed size)

**4. Is the array sorted and you need to find a pair?**
→ Two Pointers (opposite ends)

**5. Do you need to modify/filter an array in-place?**
→ Two Pointers (fast/slow)

**6. None of the above?**
→ Think about what you need: loop, set, map, etc.

---

## Mixed Problem 1: Product of All Except Self

**Problem:** Given an array, return a new array where each element is the **product of all other elements** (multiply all numbers except the one at that position).

**Input:** `[1, 2, 3, 4]`
**Output:** `[24, 12, 8, 6]`

Explanation:
- Position 0: 2×3×4 = 24 (product of everything except arr[0]=1)
- Position 1: 1×3×4 = 12 (product of everything except arr[1]=2)
- Position 2: 1×2×4 = 8
- Position 3: 1×2×3 = 6

**Constraint:** Don't use division. Don't divide the total product by each element.

**Approach: Prefix products + Suffix products**

This is the prefix sum idea, but with multiplication instead of addition.

- **Prefix product at i:** product of all elements to the LEFT of i
- **Suffix product at i:** product of all elements to the RIGHT of i
- **Answer at i:** prefix[i] × suffix[i]

```
arr = [1, 2, 3, 4]

Prefix (product of everything to the LEFT):
index 0: nothing to left → 1 (neutral for multiplication)
index 1: 1
index 2: 1 × 2 = 2
index 3: 1 × 2 × 3 = 6
prefix = [1, 1, 2, 6]

Suffix (product of everything to the RIGHT):
index 3: nothing to right → 1
index 2: 4
index 1: 3 × 4 = 12
index 0: 2 × 3 × 4 = 24
suffix = [24, 12, 4, 1]

Result:
index 0: prefix[0] × suffix[0] = 1 × 24 = 24
index 1: prefix[1] × suffix[1] = 1 × 12 = 12
index 2: prefix[2] × suffix[2] = 2 × 4 = 8
index 3: prefix[3] × suffix[3] = 6 × 1 = 6

Result = [24, 12, 8, 6] ✓
```

```javascript
function productExceptSelf(arr) {
  const n = arr.length;
  const prefix = new Array(n);
  const suffix = new Array(n);
  const result = new Array(n);

  // Build prefix products
  prefix[0] = 1;
  for (let i = 1; i < n; i++) {
    prefix[i] = prefix[i - 1] * arr[i - 1];
  }

  // Build suffix products
  suffix[n - 1] = 1;
  for (let i = n - 2; i >= 0; i--) {
    suffix[i] = suffix[i + 1] * arr[i + 1];
  }

  // Combine
  for (let i = 0; i < n; i++) {
    result[i] = prefix[i] * suffix[i];
  }

  return result;
}

console.log(productExceptSelf([1, 2, 3, 4]));  // [24, 12, 8, 6]
console.log(productExceptSelf([2, 3, 4]));      // [12, 8, 6]
```

- Time: O(n) — three separate loops (each O(n))
- Space: O(n) — three arrays of size n

---

## Mixed Problem 2: Minimum Size Subarray With Sum ≥ Target

**Problem:** Find the **length of the shortest** subarray whose sum is **at least** the target.

**Input:** `arr = [2, 3, 1, 2, 4, 3]`, `target = 7`
**Output:** `2` (subarray `[4, 3]` has sum 7, length 2)

**Technique:** Variable-size Sliding Window — but instead of longest, we want shortest.

```javascript
function minSubarrayLength(arr, target) {
  let left = 0;
  let windowSum = 0;
  let minLength = Infinity;  // start with impossibly large value

  for (let right = 0; right < arr.length; right++) {
    windowSum += arr[right];  // expand window

    // While current window meets the condition, try to shrink it
    while (windowSum >= target) {
      const currentLength = right - left + 1;
      minLength = Math.min(minLength, currentLength);  // record if smaller
      windowSum -= arr[left];  // shrink from left
      left++;
    }
  }

  return minLength === Infinity ? 0 : minLength;  // 0 if no valid subarray found
}

console.log(minSubarrayLength([2, 3, 1, 2, 4, 3], 7));  // 2
console.log(minSubarrayLength([1, 1, 1, 1], 10));        // 0 (impossible)
```

**Tracing through:**
```
arr = [2, 3, 1, 2, 4, 3], target = 7

right=0: add 2, sum=2. 2 < 7, don't shrink.
right=1: add 3, sum=5. 5 < 7, don't shrink.
right=2: add 1, sum=6. 6 < 7, don't shrink.
right=3: add 2, sum=8. 8 >= 7!
  length=4-0+1=4, minLength=4
  remove arr[0]=2, sum=6, left=1. 6 < 7, stop shrinking.
right=4: add 4, sum=10. 10 >= 7!
  length=4-1+1=4, minLength=4
  remove arr[1]=3, sum=7, left=2. 7 >= 7!
    length=4-2+1=3, minLength=3
    remove arr[2]=1, sum=6, left=3. 6 < 7, stop.
right=5: add 3, sum=9. 9 >= 7!
  length=5-3+1=3, minLength=3
  remove arr[3]=2, sum=7, left=4. 7 >= 7!
    length=5-4+1=2, minLength=2
    remove arr[4]=4, sum=3, left=5. 3 < 7, stop.

Final answer: 2 ✓
```

---

## Mixed Problem 3: Two Sum (Unsorted Array)

**Problem:** Given an array (not sorted) and a target, return the indexes of the two numbers that add up to target.

**Input:** `arr = [2, 7, 11, 15]`, `target = 9`
**Output:** `[0, 1]` (arr[0] + arr[1] = 2 + 7 = 9)

**Note:** The array is NOT sorted — so the Two Pointers (opposite ends) approach from Day 10 doesn't work here.

**Better approach: Use a Map (we'll cover Map fully in Phase 3)**

For each number, we ask: "Have I seen `target - num` before?"
- If yes → we found the pair
- If no → store this number

```javascript
function twoSum(arr, target) {
  const seen = new Map();  // stores: value → index

  for (let i = 0; i < arr.length; i++) {
    const needed = target - arr[i];  // what other number do we need?

    if (seen.has(needed)) {
      return [seen.get(needed), i];  // found it!
    }

    seen.set(arr[i], i);  // store current value and its index
  }

  return [];  // no solution
}

console.log(twoSum([2, 7, 11, 15], 9));   // [0, 1]
console.log(twoSum([3, 2, 4], 6));        // [1, 2]
console.log(twoSum([1, 5, 3, 7], 8));     // [1, 3]  (5+3? No. 1+7=8? Yes: [0,3])
```

**Trace through `[2, 7, 11, 15]`, target=9:**
```
i=0: arr[0]=2, needed=9-2=7. seen={}. 7 not in seen. Store 2→0. seen={2:0}
i=1: arr[1]=7, needed=9-7=2. seen={2:0}. 2 IS in seen! Return [seen.get(2), 1] = [0, 1] ✓
```

- Time: O(n) — one pass
- Space: O(n) — map stores up to n values

---

## The 3 Most Important Array Problems (Interview Staples)

These three problems come up constantly in interviews. If you know them cold, you're ahead:

**1. Two Sum** — use a Map (O(n))
**2. Longest Substring Without Repeating Characters** — sliding window with Set (O(n))
**3. Maximum Subarray** — a special technique called Kadane's Algorithm

### Bonus: Maximum Subarray (Kadane's Algorithm)

**Problem:** Find the contiguous subarray with the largest sum.

**Input:** `[-2, 1, -3, 4, -1, 2, 1, -5, 4]`
**Output:** `6` (subarray `[4, -1, 2, 1]` has sum 6)

**The key insight:**
At each position, you decide: is it better to
1. **Extend** the previous subarray (add the current element to existing sum), or
2. **Start fresh** from this element (reset — the previous sum was dragging us down)

```javascript
function maxSubarray(arr) {
  let currentSum = arr[0];  // sum of current subarray
  let maxSum = arr[0];      // best sum seen so far

  for (let i = 1; i < arr.length; i++) {
    // Should we extend the current subarray, or start a new one here?
    currentSum = Math.max(arr[i], currentSum + arr[i]);

    // Update best
    maxSum = Math.max(maxSum, currentSum);
  }

  return maxSum;
}

console.log(maxSubarray([-2, 1, -3, 4, -1, 2, 1, -5, 4]));  // 6
console.log(maxSubarray([1, 2, 3]));                          // 6
console.log(maxSubarray([-1, -2, -3]));                       // -1 (least negative)
```

**Trace through `[-2, 1, -3, 4, -1, 2, 1, -5, 4]`:**
```
i=0: currentSum=-2, maxSum=-2
i=1: currentSum = max(1, -2+1) = max(1, -1) = 1. maxSum=1
i=2: currentSum = max(-3, 1-3) = max(-3, -2) = -2. maxSum=1
i=3: currentSum = max(4, -2+4) = max(4, 2) = 4. maxSum=4
i=4: currentSum = max(-1, 4-1) = max(-1, 3) = 3. maxSum=4
i=5: currentSum = max(2, 3+2) = max(2, 5) = 5. maxSum=5
i=6: currentSum = max(1, 5+1) = max(1, 6) = 6. maxSum=6
i=7: currentSum = max(-5, 6-5) = max(-5, 1) = 1. maxSum=6
i=8: currentSum = max(4, 1+4) = max(4, 5) = 5. maxSum=6

Answer: 6 ✓ (from subarray [4, -1, 2, 1])
```

- Time: O(n)
- Space: O(1)

---

## Self-Check Before Moving On

Make sure you can answer these without looking:

- [ ] What is the Big O of accessing an array element by index?
- [ ] Why is adding to the beginning of an array O(n)?
- [ ] When do you use Two Pointers with opposite ends?
- [ ] When do you use Two Pointers with fast/slow?
- [ ] What is a sliding window?
- [ ] What is a fixed-size window used for vs variable-size?
- [ ] How does prefix sum work? What's the range query formula?
- [ ] What is the Two Sum trick using a Map?

If you can answer all of these, you're ready for Phase 3.

---

**Next: Day 14 — Hash Maps: What They Are and How They Work**
Phase 3 begins. Hash Maps are one of the most powerful tools in DSA — they make many O(n²) problems into O(n). And you've already seen a preview: the `Set` and `Map` used in array problems are both hash-based structures.
