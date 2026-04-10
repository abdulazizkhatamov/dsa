# Day 25 — Stacks & Queues: Mixed Problems and Review

---

## Choosing Between Stack and Queue

When you see a problem, ask:

**"Do I need to process the MOST RECENTLY added item?"**
→ Use a **Stack** (LIFO)

**"Do I need to process the OLDEST/FIRST item?"**
→ Use a **Queue** (FIFO)

Examples:
- Undo/redo → Stack (undo the LAST action)
- Browser history → Stack (go back to the LAST page)
- Customer service queue → Queue (serve the FIRST customer)
- Level-by-level tree traversal → Queue (visit nodes in order they were discovered)
- DFS (depth-first search) → Stack (go deep before going wide)
- BFS (breadth-first search) → Queue (explore neighbors before going deeper)

---

## Problem 1: Asteroid Collision

**Problem:** An array of integers represents asteroids moving in a row. Positive = moving right. Negative = moving left. Asteroids moving in the same direction never collide. When they collide: the smaller one explodes. If equal size, both explode.

**Input:** `[5, 10, -5]`
**Output:** `[5, 10]` (10 and -5 collide: 10 wins, 5 continues untouched)

**Input:** `[8, -8]`
**Output:** `[]` (both explode)

**Input:** `[10, 2, -5]`
**Output:** `[10]` (2 and -5 collide, -5 wins. Then 10 and -5 collide, 10 wins.)

**Technique:** Stack

```javascript
function asteroidCollision(asteroids) {
  const stack = [];

  for (const asteroid of asteroids) {
    let exploded = false;

    // A collision happens when: top of stack is moving right (+)
    // and current asteroid is moving left (-)
    while (
      stack.length > 0 &&
      asteroid < 0 &&
      stack[stack.length - 1] > 0
    ) {
      const top = stack[stack.length - 1];
      const abs_ast = Math.abs(asteroid);

      if (top < abs_ast) {
        stack.pop();   // top asteroid explodes, continue loop
      } else if (top === abs_ast) {
        stack.pop();   // both explode
        exploded = true;
        break;
      } else {
        exploded = true;  // current asteroid explodes
        break;
      }
    }

    if (!exploded) stack.push(asteroid);
  }

  return stack;
}

console.log(asteroidCollision([5, 10, -5]));    // [5, 10]
console.log(asteroidCollision([8, -8]));         // []
console.log(asteroidCollision([10, 2, -5]));     // [10]
console.log(asteroidCollision([-2, -1, 1, 2]));  // [-2,-1,1,2] (no collisions)
```

---

## Problem 2: Largest Rectangle in Histogram

**Problem:** Given an array where each value is the height of a bar, find the largest rectangle area.

**Input:** `[2, 1, 5, 6, 2, 3]`
**Output:** `10` (5 and 6, width 2)

This is a hard problem that uses a **Monotonic Stack** (same concept as Daily Temperatures).

**Key insight:** For each bar, find how far left and right it can extend as the shortest bar. The area is height × width.

```javascript
function largestRectangle(heights) {
  const stack = [];  // stores indexes, heights increase from bottom to top
  let maxArea = 0;

  for (let i = 0; i <= heights.length; i++) {
    const currentHeight = i === heights.length ? 0 : heights[i];

    // While current bar is shorter than the bar at the top of stack
    while (stack.length > 0 && currentHeight < heights[stack[stack.length - 1]]) {
      const height = heights[stack.pop()];
      const width = stack.length === 0 ? i : i - stack[stack.length - 1] - 1;
      maxArea = Math.max(maxArea, height * width);
    }

    stack.push(i);
  }

  return maxArea;
}

console.log(largestRectangle([2, 1, 5, 6, 2, 3]));  // 10
console.log(largestRectangle([2, 4]));               // 4
```

---

## Problem 3: Task Scheduler

**Problem:** You have tasks (represented as letters) and a cooling period `n` — the same task can't repeat until `n` slots have passed. What's the minimum total time to complete all tasks?

**Input:** `tasks = ["A","A","A","B","B","B"]`, `n = 2`
**Output:** `8` → A B C A B _ A B (or A B _ A B _ A B)

Wait, there's no C. It should be: A B _ A B _ A B = 8 slots.

**Approach:** This is a math/greedy problem, but it uses frequency counting (Map pattern).

```javascript
function leastInterval(tasks, n) {
  const freq = new Map();
  for (const task of tasks) {
    freq.set(task, (freq.get(task) || 0) + 1);
  }

  const maxFreq = Math.max(...freq.values());
  const maxCount = [...freq.values()].filter(f => f === maxFreq).length;

  // Formula: (maxFreq - 1) * (n + 1) + maxCount
  // But if tasks fill all slots, the answer is just tasks.length
  return Math.max(tasks.length, (maxFreq - 1) * (n + 1) + maxCount);
}

console.log(leastInterval(["A","A","A","B","B","B"], 2));  // 8
console.log(leastInterval(["A","A","A","B","B","B"], 0));  // 6 (no cooling needed)
console.log(leastInterval(["A","A","A","A","B","B","C"], 2));  // 10
```

---

## Phase 6 Self-Check

- [ ] What does LIFO mean? What data structure uses it?
- [ ] What does FIFO mean? What data structure uses it?
- [ ] How do you implement a stack in JavaScript?
- [ ] How do you implement a queue in JavaScript?
- [ ] What is the bracket matching algorithm? (push/pop pattern)
- [ ] What is a monotonic stack? When do you use it?
- [ ] What is a deque? What problems does it solve?
- [ ] When would you use a stack vs a queue?

---

## Phases 1-6 Summary So Far

```
Phase 0 (Days 1-4):      Math basics — exponents, logarithms, modulo
Phase 1 (Days 5-7):      Big O — measure and compare code speed
Phase 2 (Days 8-13):     Arrays — indexing, two pointers, sliding window, prefix sum
Phase 3 (Days 14-16):    Hash Maps — O(1) lookups, frequency counting
Phase 4 (Days 17-19):    Strings — manipulation, palindromes, anagrams, substrings
Phase 5 (Days 20-22):    Linked Lists — nodes/pointers, reversal, cycle detection
Phase 6 (Days 23-25):    Stacks & Queues — LIFO, FIFO, monotonic stack
```

---

**Next: Day 26 — Recursion: What It Is and How to Think About It**
Phase 7 begins. Recursion is when a function calls itself. It's a fundamentally different way to think about problems — instead of loops, you break the problem into smaller versions of itself. Trees and graphs can't really be learned without recursion.
