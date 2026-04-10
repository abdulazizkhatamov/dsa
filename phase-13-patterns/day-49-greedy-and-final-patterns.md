# Day 49 — Greedy Algorithms and Final Patterns

---

## What is a Greedy Algorithm?

A greedy algorithm makes the **locally optimal choice** at each step, hoping to reach a globally optimal solution.

Unlike DP (which considers all subproblems), greedy just picks the best option RIGHT NOW without looking back.

Greedy works when: the locally optimal choice always leads to the globally optimal solution (this can be proven but is often intuitive).

---

## Problem 1: Jump Game

**Problem:** You're at index 0 of an array. Each value is the max steps you can jump forward. Can you reach the last index?

**Input:** `[2,3,1,1,4]` → `true`
**Input:** `[3,2,1,0,4]` → `false` (always land on 0)

**Greedy:** Track the farthest index we can reach. If we pass it without reaching it, we can't go further.

```javascript
function canJump(nums) {
  let maxReach = 0;

  for (let i = 0; i < nums.length; i++) {
    if (i > maxReach) return false;  // can't reach this index
    maxReach = Math.max(maxReach, i + nums[i]);
  }

  return true;
}

console.log(canJump([2,3,1,1,4]));  // true
console.log(canJump([3,2,1,0,4]));  // false
```

---

## Problem 2: Meeting Rooms (Interval Scheduling)

**Problem:** Given a list of meeting intervals, find the maximum number of non-overlapping meetings you can attend.

**Greedy:** Sort by end time. Always take the meeting that ends earliest (leaves the most room for future meetings).

```javascript
function maxMeetings(intervals) {
  intervals.sort((a, b) => a[1] - b[1]);  // sort by end time
  let count = 1;
  let lastEnd = intervals[0][1];

  for (let i = 1; i < intervals.length; i++) {
    if (intervals[i][0] >= lastEnd) {  // starts after last ends
      count++;
      lastEnd = intervals[i][1];
    }
  }

  return count;
}
```

---

## Problem 3: Gas Station (Circular Greedy)

**Problem:** Can you complete a circular route? At each station you gain `gas[i]` fuel and spend `cost[i]` to reach the next. Find the starting station.

```javascript
function canCompleteCircuit(gas, cost) {
  let totalGain = 0;
  let currentGain = 0;
  let startStation = 0;

  for (let i = 0; i < gas.length; i++) {
    const gain = gas[i] - cost[i];
    totalGain += gain;
    currentGain += gain;

    if (currentGain < 0) {
      startStation = i + 1;  // can't start before i, try starting after
      currentGain = 0;
    }
  }

  return totalGain >= 0 ? startStation : -1;
}
```

---

## When Greedy Works vs When You Need DP

| Situation | Use |
|-----------|-----|
| Locally optimal = globally optimal | Greedy |
| You need to try multiple choices / choices depend on future | DP |
| Classic greedy: interval scheduling, activity selection | Greedy |
| Classic DP: knapsack, coin change, LCS | DP |

**Coin change with standard denominations (1, 5, 10, 25):** Greedy works!
**Coin change with arbitrary denominations:** Greedy FAILS. Need DP.

---

## The Complete Pattern Catalog

Here is everything in one place:

### Searching
- **Linear search:** O(n), no requirements
- **Binary search:** O(log n), needs sorted data
- **Hash lookup:** O(1), needs preprocessing

### Sorting
- **Bubble/Selection/Insertion:** O(n²), simple, in-place
- **Merge Sort:** O(n log n), stable, O(n) space
- **Quick Sort:** O(n log n) avg, in-place, not stable

### Array Patterns
- **Two Pointers (opposite):** sorted pair problems
- **Two Pointers (fast/slow):** remove duplicates, linked list middle
- **Sliding Window (fixed):** sum/avg of every k elements
- **Sliding Window (variable):** longest/shortest subarray with property
- **Prefix Sum:** range sum queries in O(1)
- **Monotonic Stack:** next greater/smaller element
- **Monotonic Deque:** max/min in sliding window

### Graph Patterns
- **DFS:** explore, cycle detection, connected components
- **BFS:** shortest path (unweighted), level-order
- **Topological Sort:** ordering with dependencies
- **Union-Find:** dynamic connected components

### Tree Patterns
- **DFS (recursion):** path problems, subtree problems
- **BFS (queue):** level problems, shortest path in tree
- **BST:** O(log n) search/insert (balanced)

### Other Patterns
- **Recursion:** natural for divide-and-conquer, tree, graph
- **Backtracking:** generate all possibilities, prune dead ends
- **Dynamic Programming:** overlapping subproblems, optimal substructure
- **Greedy:** locally optimal = globally optimal
- **Frequency Counter (HashMap):** counting, grouping, anagram check

---

**Next: Day 50 — The Final Day: How to Approach Any Problem**
The complete framework for solving any DSA problem in an interview — from reading the problem to analyzing complexity.
