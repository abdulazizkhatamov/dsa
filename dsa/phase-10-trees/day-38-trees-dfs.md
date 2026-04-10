# Day 38 — Trees: DFS Problems

---

## DFS on Trees

DFS (Depth-First Search) explores as far as possible down one branch before backtracking. On trees, this is naturally expressed with recursion.

Every DFS tree problem follows the same structure:
```javascript
function solve(node) {
  if (node === null) return BASE_CASE;  // handle null (leaf's children)

  const leftResult = solve(node.left);   // solve for left subtree
  const rightResult = solve(node.right); // solve for right subtree

  // Combine results to get answer for current node
  return COMBINE(leftResult, rightResult, node.val);
}
```

---

## Problem 1: Path Sum

**Problem:** Does any root-to-leaf path have a sum equal to `targetSum`?

**Input:**
```
        5
       / \
      4   8
     /   / \
    11  13  4
   /  \      \
  7    2      1
```
`targetSum = 22`
**Output:** `true` (path 5→4→11→2 = 22)

```javascript
function hasPathSum(root, targetSum) {
  if (root === null) return false;

  // At a leaf: check if remaining sum equals leaf value
  if (!root.left && !root.right) {
    return root.val === targetSum;
  }

  // Recurse with reduced sum
  return hasPathSum(root.left, targetSum - root.val) ||
         hasPathSum(root.right, targetSum - root.val);
}
```

---

## Problem 2: All Root-to-Leaf Paths

**Problem:** Return all root-to-leaf paths as strings.

**Output for tree above:** `["5->4->11->7", "5->4->11->2", "5->8->13", "5->8->4->1"]`

```javascript
function binaryTreePaths(root) {
  const result = [];

  function dfs(node, path) {
    if (!node) return;

    path += node.val;  // add current node to path

    if (!node.left && !node.right) {
      result.push(path);  // leaf reached — save path
      return;
    }

    path += '->';                   // not a leaf — add arrow
    dfs(node.left, path);
    dfs(node.right, path);
  }

  dfs(root, '');
  return result;
}
```

---

## Problem 3: Maximum Path Sum

**Problem:** A path is any sequence of nodes (not just root to leaf). Find the path with the maximum sum.

**Input:**
```
    -10
    /  \
   9    20
       /  \
      15    7
```
**Output:** `42` (path 15→20→7)

**Key insight:** For each node, the maximum path THROUGH that node is:
`node.val + max(leftGain, 0) + max(rightGain, 0)`

We add 0 (not the subtree) if the subtree would decrease our sum.

```javascript
function maxPathSum(root) {
  let globalMax = -Infinity;

  function maxGain(node) {
    if (!node) return 0;

    // Maximum gain from left/right subtrees (ignore if negative)
    const leftGain = Math.max(maxGain(node.left), 0);
    const rightGain = Math.max(maxGain(node.right), 0);

    // Path through this node
    const pathThroughNode = node.val + leftGain + rightGain;

    // Update global maximum
    globalMax = Math.max(globalMax, pathThroughNode);

    // Return the gain we can contribute to our parent
    // (can only go in one direction from parent's perspective)
    return node.val + Math.max(leftGain, rightGain);
  }

  maxGain(root);
  return globalMax;
}
```

---

## Problem 4: Diameter of a Binary Tree

**Problem:** The diameter is the length of the longest path between any two nodes (path = number of edges).

```javascript
function diameterOfBinaryTree(root) {
  let diameter = 0;

  function height(node) {
    if (!node) return 0;

    const leftHeight = height(node.left);
    const rightHeight = height(node.right);

    // The diameter passing through this node
    diameter = Math.max(diameter, leftHeight + rightHeight);

    return 1 + Math.max(leftHeight, rightHeight);
  }

  height(root);
  return diameter;
}
```

---

## Problem 5: Lowest Common Ancestor (General Binary Tree)

Unlike BST (Day 37), here we can't use the BST property. We search both sides.

```javascript
function lowestCommonAncestor(root, p, q) {
  if (!root || root === p || root === q) return root;

  const left = lowestCommonAncestor(root.left, p, q);
  const right = lowestCommonAncestor(root.right, p, q);

  // If found in both subtrees → current node is LCA
  if (left && right) return root;

  // Otherwise return whichever side found something
  return left || right;
}
```

---

## Problem 6: Flatten Binary Tree to Linked List

**Problem:** Flatten the tree in-place to a "linked list" (right-skewed tree) in pre-order.

```
     1           1
    / \            \
   2   5    →       2
  / \   \            \
 3   4   6            3
                       \
                        4
                         \
                          5
                           \
                            6
```

```javascript
function flatten(root) {
  if (!root) return;

  flatten(root.left);
  flatten(root.right);

  // Save right subtree
  const rightSubtree = root.right;

  // Move left subtree to right
  root.right = root.left;
  root.left = null;

  // Find the end of the (formerly left) subtree
  let current = root;
  while (current.right) current = current.right;

  // Attach original right subtree
  current.right = rightSubtree;
}
```

---

## The DFS Decision at Each Node

Every tree DFS problem asks: **what do I compute at this node given what I get from children?**

| Problem | What to Return | What to Track Globally |
|---------|---------------|------------------------|
| Max depth | Height of subtree | — |
| Path sum | Whether path exists | — |
| Max path sum | Max gain in one direction | Global max path |
| Diameter | Height of subtree | Global diameter |
| Check balanced | Height (or -1 if unbalanced) | — |

---

**Next: Day 39 — Trees: BFS Problems**
Level-order traversal and problems that require processing nodes level by level.
