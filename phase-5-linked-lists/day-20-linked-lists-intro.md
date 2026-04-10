# Day 20 — Linked Lists: What They Are

---

## The Problem With Arrays

Arrays are stored in a continuous block of memory. That's why index access is O(1) — you calculate the exact address.

But it also means:
- **Inserting at the beginning** requires shifting every element → O(n)
- **Deleting from the beginning** requires shifting every element → O(n)
- If you want a bigger array, you might need to find a new continuous block

---

## What is a Linked List?

A Linked List stores items in **nodes**. Each node holds:
1. A **value** (the data)
2. A **pointer** to the NEXT node

The nodes do NOT have to be next to each other in memory. Each one points to where the next one lives.

```
Node 1          Node 2          Node 3          Node 4
┌────────┐      ┌────────┐      ┌────────┐      ┌────────┐
│ val: 1 │ ───> │ val: 2 │ ───> │ val: 3 │ ───> │ val: 4 │ ───> null
└────────┘      └────────┘      └────────┘      └────────┘
    ↑
   head (the first node)
```

- `head` = the pointer to the first node
- The last node points to `null` (meaning: nothing after this)

---

## How Nodes Are Built in JavaScript

```javascript
class ListNode {
  constructor(val) {
    this.val = val;
    this.next = null;  // points to nothing by default
  }
}

// Build the list: 1 → 2 → 3 → 4
const node1 = new ListNode(1);
const node2 = new ListNode(2);
const node3 = new ListNode(3);
const node4 = new ListNode(4);

node1.next = node2;
node2.next = node3;
node3.next = node4;
// node4.next is already null

const head = node1;  // head is our entry point
```

Now:
- `head.val` → `1`
- `head.next.val` → `2`
- `head.next.next.val` → `3`
- `head.next.next.next.val` → `4`
- `head.next.next.next.next` → `null`

---

## Traversing a Linked List

You can't jump to index 3 directly — you must start at `head` and follow the pointers.

```javascript
function printList(head) {
  let current = head;  // start at the beginning

  while (current !== null) {
    console.log(current.val);
    current = current.next;  // move to next node
  }
}

printList(head);  // prints: 1, 2, 3, 4
```

- Time: O(n) — must visit every node
- Space: O(1) — just one `current` pointer variable

---

## Adding a Node at the Beginning — O(1)

This is where Linked Lists shine. Adding to the front is just three steps:
1. Create a new node
2. Point new node's next to current head
3. Update head to new node

```javascript
function addToFront(head, val) {
  const newNode = new ListNode(val);
  newNode.next = head;  // new node points to old head
  return newNode;       // new node IS the new head
}

let head = node1;  // list: 1 → 2 → 3 → 4
head = addToFront(head, 0);
// list: 0 → 1 → 2 → 3 → 4
```

No shifting! No moving existing nodes. Just reconnect two pointers. O(1).

---

## Adding a Node at the End — O(n)

Must traverse to the last node first.

```javascript
function addToEnd(head, val) {
  const newNode = new ListNode(val);

  if (head === null) return newNode;  // empty list — new node is the head

  let current = head;
  while (current.next !== null) {   // walk until last node
    current = current.next;
  }

  current.next = newNode;  // attach new node at the end
  return head;
}
```

O(n) because you have to traverse to the end.

---

## Removing the First Node — O(1)

```javascript
function removeFromFront(head) {
  if (head === null) return null;
  return head.next;  // new head is the second node
}

// list: 1 → 2 → 3 → 4
head = removeFromFront(head);
// list: 2 → 3 → 4
```

Again, O(1) — just move the head pointer.

---

## Removing a Specific Node — O(n)

You need to find the node BEFORE the one you want to delete, then skip over it.

```
Before: 1 → 2 → 3 → 4
Remove 3:
         1 → 2 → 4  (skip 3 by connecting 2 directly to 4)
```

```javascript
function removeValue(head, val) {
  if (head === null) return null;

  // Special case: removing the head
  if (head.val === val) return head.next;

  let current = head;
  while (current.next !== null) {
    if (current.next.val === val) {
      current.next = current.next.next;  // skip the target node
      return head;
    }
    current = current.next;
  }

  return head;  // value not found
}
```

O(n) — may need to traverse entire list.

---

## Big O Comparison: Array vs Linked List

| Operation | Array | Linked List |
|-----------|-------|-------------|
| Access by index | **O(1)** | O(n) |
| Search (find value) | O(n) | O(n) |
| Add to beginning | O(n) | **O(1)** |
| Add to end | O(1) (amortized) | O(n) |
| Remove from beginning | O(n) | **O(1)** |
| Remove from end | O(1) | O(n) |
| Insert in middle | O(n) | O(n) (find) + O(1) (insert) |

**Use a Linked List when:** you need frequent insertions/deletions at the beginning.
**Use an Array when:** you need frequent index-based access.

---

## Getting the Length of a Linked List

```javascript
function getLength(head) {
  let count = 0;
  let current = head;

  while (current !== null) {
    count++;
    current = current.next;
  }

  return count;
}
```

O(n) — must count every node. (Unlike arrays, there's no `.length` property.)

---

## Converting Array to Linked List

In most interview problems, you'll be given a linked list already built. But it helps to be able to create one quickly from an array:

```javascript
function arrayToList(arr) {
  if (arr.length === 0) return null;

  const head = new ListNode(arr[0]);
  let current = head;

  for (let i = 1; i < arr.length; i++) {
    current.next = new ListNode(arr[i]);
    current = current.next;
  }

  return head;
}

// Convert list back to array (for easy printing)
function listToArray(head) {
  const result = [];
  let current = head;
  while (current !== null) {
    result.push(current.val);
    current = current.next;
  }
  return result;
}

const head = arrayToList([1, 2, 3, 4, 5]);
console.log(listToArray(head));  // [1, 2, 3, 4, 5]
```

---

## Summary

| Concept | Key Point |
|---------|-----------|
| Node | Contains value + pointer to next |
| Head | Pointer to the first node |
| Tail | Last node, points to null |
| Traversal | Follow `.next` pointers from head |
| No index access | Must traverse from head every time |
| Insert at front | O(1) — just reconnect pointers |
| Remove at front | O(1) — just move head |
| Everything else | O(n) — requires traversal |

---

## Practice Problems

**Practice 1:** Write a function that returns the value of the last node in a linked list.
```
Input: 1 → 2 → 3 → 4
Output: 4
```

**Practice 2:** Write a function that counts how many nodes have a value greater than `x`.
```
Input: 1 → 5 → 2 → 8 → 3, x = 3
Output: 2  (5 and 8 are > 3)
```

---

## Answers

**Practice 1:**
```javascript
function getLastValue(head) {
  if (head === null) return null;

  let current = head;
  while (current.next !== null) {
    current = current.next;
  }
  return current.val;
}
```

**Practice 2:**
```javascript
function countGreaterThan(head, x) {
  let count = 0;
  let current = head;

  while (current !== null) {
    if (current.val > x) count++;
    current = current.next;
  }

  return count;
}
```

---

**Next: Day 21 — Linked Lists: Reversal and Two Pointers**
Two of the most common linked list interview operations — reverse a list, and use two pointers to find the middle or detect patterns.
