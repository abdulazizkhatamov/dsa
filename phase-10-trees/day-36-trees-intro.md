# Day 36 — Trees: Introduction

---

## What is a Tree?

A tree is a hierarchical data structure. Unlike a linked list (which is linear — one node leads to the next), a tree branches out.

Real-world examples:
- A family tree: one grandparent → multiple children → multiple grandchildren
- A file system: root folder → subfolders → files
- An HTML document: `<html>` → `<body>` → paragraphs, divs, etc.

```
        1          ← root (top of tree, no parent)
       / \
      2   3        ← children of 1
     / \   \
    4   5   6      ← leaves (no children)
```

---

## Tree Terminology

| Term | Meaning |
|------|---------|
| **Root** | The top node — has no parent |
| **Node** | Any element in the tree |
| **Parent** | A node that has children |
| **Child** | A node connected below a parent |
| **Leaf** | A node with no children |
| **Edge** | The connection between two nodes |
| **Height** | The number of edges from root to the deepest leaf |
| **Depth** | The number of edges from the root to a specific node |
| **Subtree** | A node and all its descendants |

```
        1     ← depth 0, root
       / \
      2   3   ← depth 1
     / \
    4   5     ← depth 2, leaves
```

Height of the tree above = 2 (root to deepest leaf = two edges).

---

## Binary Tree

A **binary tree** is a tree where every node has **at most 2 children** — called **left** and **right**.

```javascript
class TreeNode {
  constructor(val) {
    this.val = val;
    this.left = null;
    this.right = null;
  }
}

// Build this tree:
//       1
//      / \
//     2   3
//    / \
//   4   5

const root = new TreeNode(1);
root.left = new TreeNode(2);
root.right = new TreeNode(3);
root.left.left = new TreeNode(4);
root.left.right = new TreeNode(5);
```

---

## Tree Traversals

A traversal visits every node exactly once. There are 4 main ways:

### 1. In-Order (Left → Root → Right)

Visit the left subtree, then the current node, then the right subtree.

```javascript
function inOrder(node) {
  if (node === null) return;
  inOrder(node.left);       // visit left subtree
  console.log(node.val);    // visit current node
  inOrder(node.right);      // visit right subtree
}

inOrder(root);  // 4, 2, 5, 1, 3
```

### 2. Pre-Order (Root → Left → Right)

Visit the current node FIRST, then subtrees.

```javascript
function preOrder(node) {
  if (node === null) return;
  console.log(node.val);    // visit current node FIRST
  preOrder(node.left);
  preOrder(node.right);
}

preOrder(root);  // 1, 2, 4, 5, 3
```

### 3. Post-Order (Left → Right → Root)

Visit both subtrees BEFORE the current node.

```javascript
function postOrder(node) {
  if (node === null) return;
  postOrder(node.left);
  postOrder(node.right);
  console.log(node.val);    // visit current node LAST
}

postOrder(root);  // 4, 5, 2, 3, 1
```

### 4. Level-Order (BFS — Breadth-First)

Visit nodes level by level, left to right.

```javascript
function levelOrder(root) {
  if (!root) return [];

  const result = [];
  const queue = [root];

  while (queue.length > 0) {
    const node = queue.shift();  // dequeue
    result.push(node.val);

    if (node.left) queue.push(node.left);   // enqueue children
    if (node.right) queue.push(node.right);
  }

  return result;
}

levelOrder(root);  // [1, 2, 3, 4, 5]
```

---

## Memory Aid: DFS vs BFS

**DFS (Depth-First Search):** Go as DEEP as possible first. Uses recursion (or a stack).
- Pre-order, In-order, Post-order are all DFS

**BFS (Breadth-First Search):** Go as WIDE as possible first. Uses a queue.
- Level-order is BFS

```
Tree:     1
         / \
        2   3
       / \
      4   5

DFS (pre-order): 1, 2, 4, 5, 3   ← goes deep left first
BFS (level):     1, 2, 3, 4, 5   ← visits each level before going deeper
```

---

## Basic Tree Problems

### Problem 1: Maximum Depth of a Tree

```javascript
function maxDepth(root) {
  if (root === null) return 0;  // empty tree

  const leftDepth = maxDepth(root.left);
  const rightDepth = maxDepth(root.right);

  return 1 + Math.max(leftDepth, rightDepth);
}

console.log(maxDepth(root));  // 3
```

**Trace:**
```
maxDepth(1)
= 1 + max(maxDepth(2), maxDepth(3))
= 1 + max(
    1 + max(maxDepth(4), maxDepth(5)),
    1 + max(null, null)
  )
= 1 + max(1 + max(1, 1), 1 + max(0, 0))
= 1 + max(1 + 1, 1 + 0)
= 1 + max(2, 1)
= 1 + 2 = 3 ✓
```

### Problem 2: Check if Tree is Symmetric

```javascript
function isSymmetric(root) {
  if (!root) return true;

  function isMirror(left, right) {
    if (!left && !right) return true;   // both null → symmetric
    if (!left || !right) return false;  // one null, one not → not symmetric

    return left.val === right.val &&
           isMirror(left.left, right.right) &&
           isMirror(left.right, right.left);
  }

  return isMirror(root.left, root.right);
}
```

---

## Summary

| Traversal | Order | Uses | Technique |
|-----------|-------|------|-----------|
| In-order | Left, Root, Right | Print BST in sorted order | DFS/recursion |
| Pre-order | Root, Left, Right | Copy a tree, serialize | DFS/recursion |
| Post-order | Left, Right, Root | Delete a tree, evaluate expression | DFS/recursion |
| Level-order | Level by level | Find shortest path, BFS problems | Queue |

---

**Next: Day 37 — Binary Search Trees**
A special binary tree where left child < parent < right child. This property enables O(log n) search, insert, and delete.
