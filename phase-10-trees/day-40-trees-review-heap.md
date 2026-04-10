# Day 40 — Trees: Review + Heap Introduction

---

## Phase 10 Self-Check

- [ ] What is a tree? What is the root, leaf, height, depth?
- [ ] What is a binary tree? What is a BST?
- [ ] What are the 4 traversals? Write their order from memory.
- [ ] When would you use BFS vs DFS on a tree?
- [ ] What is the BST property? Why does it enable O(log n) operations?
- [ ] How do you validate a BST? (hint: min/max boundaries)
- [ ] What is the lowest common ancestor?
- [ ] How does level-order BFS work? (hint: queue + levelSize)

---

## What is a Heap?

A **Heap** is a special binary tree that satisfies:
1. It's a **complete binary tree** — all levels are filled left to right
2. The **Heap property** — every parent is larger (max-heap) or smaller (min-heap) than its children

**Max-Heap:** parent ≥ children (root is the maximum)
**Min-Heap:** parent ≤ children (root is the minimum)

```
Max-Heap:
        9
       / \
      7   8
     / \ / \
    5  6 4  2
```

**Key property:** The root is always the maximum (max-heap) or minimum (min-heap). This makes it perfect for a **Priority Queue** — always get the highest/lowest priority item in O(1).

---

## Heap as an Array

A complete binary tree can be stored in an array without pointers!

```
Array: [9, 7, 8, 5, 6, 4, 2]
Index:  0  1  2  3  4  5  6

For node at index i:
  Left child:  2*i + 1
  Right child: 2*i + 2
  Parent:      Math.floor((i-1) / 2)
```

```
index 0 (9): left=index1(7), right=index2(8)
index 1 (7): left=index3(5), right=index4(6)
index 2 (8): left=index5(4), right=index6(2)
```

---

## Heap Operations

### Insert — O(log n)

1. Add to the end of the array
2. **Bubble up**: swap with parent while child > parent (for max-heap)

```javascript
class MaxHeap {
  constructor() {
    this.heap = [];
  }

  insert(val) {
    this.heap.push(val);
    this._bubbleUp(this.heap.length - 1);
  }

  _bubbleUp(index) {
    while (index > 0) {
      const parentIndex = Math.floor((index - 1) / 2);
      if (this.heap[parentIndex] >= this.heap[index]) break;

      [this.heap[parentIndex], this.heap[index]] = [this.heap[index], this.heap[parentIndex]];
      index = parentIndex;
    }
  }
}
```

### Extract Max — O(log n)

1. The max is at index 0
2. Move the last element to index 0
3. **Bubble down**: swap with the larger child while parent < child

```javascript
  extractMax() {
    if (this.heap.length === 0) return null;

    const max = this.heap[0];
    const last = this.heap.pop();

    if (this.heap.length > 0) {
      this.heap[0] = last;
      this._bubbleDown(0);
    }

    return max;
  }

  _bubbleDown(index) {
    const n = this.heap.length;

    while (true) {
      let largest = index;
      const left = 2 * index + 1;
      const right = 2 * index + 2;

      if (left < n && this.heap[left] > this.heap[largest]) largest = left;
      if (right < n && this.heap[right] > this.heap[largest]) largest = right;

      if (largest === index) break;

      [this.heap[index], this.heap[largest]] = [this.heap[largest], this.heap[index]];
      index = largest;
    }
  }
```

---

## Heap Use Cases

| Use Case | Which Heap | Why |
|----------|-----------|-----|
| Always get the maximum | Max-Heap | Root = max, O(1) peek, O(log n) extract |
| Always get the minimum | Min-Heap | Root = min, O(1) peek, O(log n) extract |
| Kth largest element | Min-Heap of size k | Keep k largest; root = kth largest |
| Kth smallest element | Max-Heap of size k | Keep k smallest; root = kth smallest |
| Median maintenance | Both heaps | Left = max-heap, right = min-heap |

---

## Problem: Kth Largest Using a Min-Heap

**Problem:** Find the kth largest element in a stream of numbers.

**Approach:** Maintain a min-heap of the k largest numbers seen so far. The root (minimum of the heap) is always the kth largest.

```javascript
// Simplified version (using sorted array as mock heap)
function findKthLargest(nums, k) {
  // Sort descending and take kth
  nums.sort((a, b) => b - a);
  return nums[k - 1];
}

// Real heap approach: O(n log k)
function findKthLargestHeap(nums, k) {
  // JavaScript doesn't have a built-in heap,
  // but this is the logic:
  // 1. Build a min-heap from first k elements
  // 2. For each remaining element, if it's larger than heap root,
  //    replace root and heapify
  // 3. Return root = kth largest

  // In interviews, explain the approach even if you use sort
  nums.sort((a, b) => b - a);
  return nums[k - 1];
}
```

---

## Phase 10 Summary

```
Day 36: Trees intro — terminology, traversals (pre/in/post/level)
Day 37: BST — property, search/insert/delete, validate
Day 38: DFS problems — path sum, max path, diameter, LCA
Day 39: BFS problems — level order, min depth, right view
Day 40: Review + Heap — heap property, array representation, O(log n) insert/extract
```

---

**Next: Day 41 — Graphs: Introduction**
Phase 11 begins. Graphs are the most general data structure — trees are a special type of graph. Graphs can have cycles, multiple edges, directed or undirected connections. Used for social networks, maps, dependencies, and more.
