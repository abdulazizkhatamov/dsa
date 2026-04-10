# Day 39 — Trees: BFS Problems

---

## BFS on Trees

BFS (Breadth-First Search) processes nodes level by level. It uses a **queue** — add children to the queue, then process nodes in the order they were added.

**The BFS template:**
```javascript
function bfs(root) {
  if (!root) return;
  const queue = [root];

  while (queue.length > 0) {
    const node = queue.shift();

    // Process node
    console.log(node.val);

    // Add children for next level
    if (node.left) queue.push(node.left);
    if (node.right) queue.push(node.right);
  }
}
```

**Level-by-level BFS** (process one entire level before the next):
```javascript
function levelOrderBFS(root) {
  if (!root) return [];
  const result = [];
  const queue = [root];

  while (queue.length > 0) {
    const levelSize = queue.length;  // how many nodes in this level
    const level = [];

    for (let i = 0; i < levelSize; i++) {
      const node = queue.shift();
      level.push(node.val);
      if (node.left) queue.push(node.left);
      if (node.right) queue.push(node.right);
    }

    result.push(level);
  }

  return result;
}
```

---

## Problem 1: Level Order Traversal

**Problem:** Return values level by level as a 2D array.

```javascript
function levelOrder(root) {
  if (!root) return [];
  const result = [];
  const queue = [root];

  while (queue.length > 0) {
    const levelSize = queue.length;
    const level = [];

    for (let i = 0; i < levelSize; i++) {
      const node = queue.shift();
      level.push(node.val);
      if (node.left) queue.push(node.left);
      if (node.right) queue.push(node.right);
    }

    result.push(level);
  }

  return result;
}

// For tree: 3 → [9,20] → [null,null,15,7]
// Output: [[3],[9,20],[15,7]]
```

---

## Problem 2: Minimum Depth

**Problem:** Find the minimum depth — the number of nodes on the shortest root-to-leaf path.

BFS is PERFECT for this: the first leaf we encounter is at the minimum depth.

```javascript
function minDepth(root) {
  if (!root) return 0;
  const queue = [[root, 1]];  // [node, depth]

  while (queue.length > 0) {
    const [node, depth] = queue.shift();

    // First leaf we encounter = minimum depth
    if (!node.left && !node.right) return depth;

    if (node.left) queue.push([node.left, depth + 1]);
    if (node.right) queue.push([node.right, depth + 1]);
  }
}
```

**Why BFS and not DFS?** DFS would find ALL leaves and track the minimum. BFS stops at the FIRST leaf it finds — which is guaranteed to be at minimum depth because BFS processes level by level.

---

## Problem 3: Right Side View

**Problem:** Imagine standing to the right of a tree. Return the values you can see (rightmost node at each level).

```
        1         ← see 1
       / \
      2   3       ← see 3
       \
        5         ← see 5

Output: [1, 3, 5]
```

```javascript
function rightSideView(root) {
  if (!root) return [];
  const result = [];
  const queue = [root];

  while (queue.length > 0) {
    const levelSize = queue.length;

    for (let i = 0; i < levelSize; i++) {
      const node = queue.shift();

      if (i === levelSize - 1) {
        result.push(node.val);  // last node in level = rightmost
      }

      if (node.left) queue.push(node.left);
      if (node.right) queue.push(node.right);
    }
  }

  return result;
}
```

---

## Problem 4: Level Averages

**Problem:** Return the average value at each level.

```javascript
function averageOfLevels(root) {
  if (!root) return [];
  const result = [];
  const queue = [root];

  while (queue.length > 0) {
    const levelSize = queue.length;
    let levelSum = 0;

    for (let i = 0; i < levelSize; i++) {
      const node = queue.shift();
      levelSum += node.val;
      if (node.left) queue.push(node.left);
      if (node.right) queue.push(node.right);
    }

    result.push(levelSum / levelSize);
  }

  return result;
}
```

---

## DFS vs BFS for Trees: When to Use Each

| Use DFS when... | Use BFS when... |
|----------------|----------------|
| Working on a single path (root to leaf) | Need level-by-level processing |
| Need to explore entire subtrees | Need shortest path |
| Tree structure mirrors recursion | Need rightmost/leftmost at each level |
| Post-order needed (process children first) | Need to process nodes in discovery order |

---

**Next: Day 40 — Trees: Review + Heap Introduction**
Wrapping up trees and introducing the Heap — a special complete binary tree used for priority queues and the HeapSort algorithm.
