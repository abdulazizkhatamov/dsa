# Day 37 — Binary Search Trees

---

## What is a BST?

A Binary Search Tree (BST) is a binary tree with one special rule:

**For every node:**
- All values in its LEFT subtree are SMALLER
- All values in its RIGHT subtree are LARGER

```
        8
       / \
      3   10
     / \    \
    1   6    14
       / \   /
      4   7 13
```

Check node 3: left(1) < 3 < right(6) ✓
Check node 8: left(3,1,6,4,7) all < 8 < right(10,14,13) all ✓

This property enables fast operations — instead of checking every node, you eliminate half the tree at each step (like binary search on arrays).

---

## Search in a BST: O(log n) average

```javascript
function searchBST(root, target) {
  if (root === null) return null;  // not found
  if (root.val === target) return root;

  if (target < root.val) {
    return searchBST(root.left, target);   // go left
  } else {
    return searchBST(root.right, target);  // go right
  }
}

// Iterative version (no recursion):
function searchBSTIterative(root, target) {
  let current = root;

  while (current !== null) {
    if (current.val === target) return current;
    if (target < current.val) current = current.left;
    else current = current.right;
  }

  return null;
}
```

**Trace searching for 6:**
```
Start at 8: 6 < 8 → go left
At 3: 6 > 3 → go right
At 6: 6 === 6 → found! ✓
```

3 steps to find 6 in a 9-node tree. Binary search on a tree.

---

## Insert into a BST: O(log n) average

```javascript
function insertBST(root, val) {
  if (root === null) return new TreeNode(val);  // found the right spot

  if (val < root.val) {
    root.left = insertBST(root.left, val);   // insert into left subtree
  } else if (val > root.val) {
    root.right = insertBST(root.right, val); // insert into right subtree
  }
  // if val === root.val: ignore (no duplicates)

  return root;
}
```

**Trace inserting 5:**
```
At 8: 5 < 8 → go left
At 3: 5 > 3 → go right
At 6: 5 < 6 → go left
At null → create node(5), attach as left child of 6 ✓
```

---

## Delete from a BST

Deletion has 3 cases:

**Case 1:** Node has NO children (leaf) → just remove it

**Case 2:** Node has ONE child → replace node with its child

**Case 3:** Node has TWO children → find the in-order successor (smallest node in right subtree), replace the node's value, then delete the successor

```javascript
function deleteBST(root, val) {
  if (root === null) return null;

  if (val < root.val) {
    root.left = deleteBST(root.left, val);
  } else if (val > root.val) {
    root.right = deleteBST(root.right, val);
  } else {
    // Found the node to delete

    // Case 1 & 2: no children or one child
    if (!root.left) return root.right;
    if (!root.right) return root.left;

    // Case 3: two children
    // Find the in-order successor (smallest in right subtree)
    let successor = root.right;
    while (successor.left !== null) {
      successor = successor.left;
    }

    root.val = successor.val;  // replace value
    root.right = deleteBST(root.right, successor.val);  // delete successor
  }

  return root;
}
```

---

## In-Order Traversal of BST = Sorted Order

This is a crucial property: if you do an in-order traversal of a BST, you get all values in **ascending sorted order**.

```
        8
       / \
      3   10
     / \    \
    1   6    14

In-order: 1, 3, 6, 8, 10, 14  ← sorted! ✓
```

This is used to:
- Validate if a tree is a BST
- Find the kth smallest element
- Convert BST to sorted array

---

## Validate a BST

**Problem:** Check if a binary tree is a valid BST.

**Key:** Don't just check that each node is bigger than its left child and smaller than its right. The entire LEFT subtree must be smaller, and the entire RIGHT subtree must be larger.

```
Invalid BST (tricky case):
        5
       / \
      4   6
     / \
    3   7    ← 7 is bigger than 5, but it's in the left subtree! INVALID
```

**Solution:** Pass min and max boundaries down through the recursion.

```javascript
function isValidBST(root, min = -Infinity, max = Infinity) {
  if (root === null) return true;

  if (root.val <= min || root.val >= max) return false;

  return isValidBST(root.left, min, root.val) &&   // left must be < current
         isValidBST(root.right, root.val, max);    // right must be > current
}
```

---

## BST Big O

| Operation | Average | Worst (unbalanced) |
|-----------|---------|-------------------|
| Search | O(log n) | O(n) |
| Insert | O(log n) | O(n) |
| Delete | O(log n) | O(n) |
| Min/Max | O(log n) | O(n) |

**Worst case O(n):** A BST can become a linked list if elements are inserted in sorted order.

```
Insert 1,2,3,4,5 in order:
1
 \
  2
   \
    3
     \
      4
       \
        5
```

This is why **balanced** BSTs exist (AVL trees, Red-Black trees) — they automatically keep the tree balanced so operations stay O(log n). In interviews, you generally assume a "balanced" BST unless told otherwise.

---

## Practice Problems

**Practice 1:** Find the k-th smallest element in a BST.
```
Input: BST from above, k=3
Output: 6 (in-order: 1,3,6,8,10,14 — 3rd is 6)
```

**Practice 2:** Find the lowest common ancestor of two nodes in a BST.
```
The lowest common ancestor of nodes p and q is the deepest node that is an ancestor of both.
```

---

## Answers

**Practice 1:**
```javascript
function kthSmallest(root, k) {
  let count = 0;
  let result = null;

  function inOrder(node) {
    if (!node || result !== null) return;
    inOrder(node.left);
    count++;
    if (count === k) {
      result = node.val;
      return;
    }
    inOrder(node.right);
  }

  inOrder(root);
  return result;
}
```

**Practice 2:**
```javascript
function lowestCommonAncestor(root, p, q) {
  // Use BST property: if both are smaller, go left; if both larger, go right
  if (p.val < root.val && q.val < root.val) {
    return lowestCommonAncestor(root.left, p, q);
  }
  if (p.val > root.val && q.val > root.val) {
    return lowestCommonAncestor(root.right, p, q);
  }
  // One is smaller and one is larger — or one of them IS root
  return root;
}
```

---

**Next: Day 38 — Trees: DFS Problems**
Using depth-first search to solve common tree interview problems: path sums, tree diameter, and more.
