# Day 21 — Linked Lists: Reversal and Two Pointers

---

## Reversing a Linked List

Reversing a linked list is one of the most common interview problems. It looks intimidating at first, but the idea is simple once you see it.

**Before:**
```
1 → 2 → 3 → 4 → null
```

**After:**
```
null ← 1 ← 2 ← 3 ← 4
(head now points to 4)
```

We need to flip every arrow.

---

## The Iterative Approach

We use **three pointers**:
- `prev` — the node we just processed (starts as `null`)
- `current` — the node we're currently reversing
- `next` — temporarily saves where we're going before we redirect

```
Step by step through: 1 → 2 → 3 → 4

Initially:
prev=null, current=1, next=?

Step 1:
  next = current.next = 2        (save where we're going)
  current.next = prev = null     (reverse the arrow: 1 now points to null)
  prev = current = 1             (move prev forward)
  current = next = 2             (move current forward)

  State: null ← 1   2 → 3 → 4
                ↑   ↑
               prev curr

Step 2:
  next = current.next = 3
  current.next = prev = 1        (2 now points back to 1)
  prev = 2
  current = 3

  State: null ← 1 ← 2   3 → 4
                      ↑  ↑
                     prev curr

Step 3:
  next = 4
  current.next = prev = 2        (3 → 2)
  prev = 3
  current = 4

Step 4:
  next = null
  current.next = prev = 3        (4 → 3)
  prev = 4
  current = null  → LOOP ENDS

New head = prev = 4
```

```javascript
function reverseList(head) {
  let prev = null;
  let current = head;

  while (current !== null) {
    const next = current.next;   // save next before we overwrite it
    current.next = prev;          // reverse the pointer
    prev = current;               // advance prev
    current = next;               // advance current
  }

  return prev;  // prev is the new head
}

// Test it:
const head = arrayToList([1, 2, 3, 4, 5]);
const reversed = reverseList(head);
console.log(listToArray(reversed));  // [5, 4, 3, 2, 1]
```

- Time: O(n) — one pass
- Space: O(1) — just three pointer variables

---

## Reversing a Sublist (from position m to n)

**Problem:** Reverse only the nodes between positions `m` and `n` (1-indexed).

**Input:** `1 → 2 → 3 → 4 → 5`, m=2, n=4
**Output:** `1 → 4 → 3 → 2 → 5`

```javascript
function reverseBetween(head, m, n) {
  if (!head || m === n) return head;

  // Create a dummy node before head to simplify edge cases
  const dummy = new ListNode(0);
  dummy.next = head;
  let prev = dummy;

  // Move prev to the node just before position m
  for (let i = 1; i < m; i++) {
    prev = prev.next;
  }

  // current is at position m — the start of reversal
  let current = prev.next;

  // Reverse n-m times
  for (let i = 0; i < n - m; i++) {
    const next = current.next;
    current.next = next.next;
    next.next = prev.next;
    prev.next = next;
  }

  return dummy.next;
}
```

---

## Two Pointers on Linked Lists

Just like with arrays, we can use two pointers on linked lists. The most common patterns are:

### Pattern 1: Find the Middle Node

Use **slow and fast** pointers:
- `slow` moves 1 step at a time
- `fast` moves 2 steps at a time
- When `fast` reaches the end, `slow` is at the middle

```
List: 1 → 2 → 3 → 4 → 5

Start: slow=1, fast=1

Step 1: slow → 2, fast → 3 (fast moved 2)
Step 2: slow → 3, fast → 5 (fast moved 2)
Step 3: fast.next = null → stop

slow is at 3 — the middle ✓
```

```javascript
function findMiddle(head) {
  let slow = head;
  let fast = head;

  while (fast !== null && fast.next !== null) {
    slow = slow.next;        // 1 step
    fast = fast.next.next;   // 2 steps
  }

  return slow;  // slow is at the middle
}

const head = arrayToList([1, 2, 3, 4, 5]);
console.log(findMiddle(head).val);  // 3

const head2 = arrayToList([1, 2, 3, 4]);
console.log(findMiddle(head2).val);  // 2  (first of two middle nodes)
```

**Why does this work?**
Fast travels twice as far as slow. When fast reaches the end (n steps), slow has taken n/2 steps — the midpoint.

---

### Pattern 2: Find the Kth Node From the End

**Problem:** Find the k-th node from the end of the list.

**Input:** `1 → 2 → 3 → 4 → 5`, k=2
**Output:** `4` (4 is the 2nd from the end)

**Trick:** Use two pointers that are `k` positions apart. When the front pointer reaches the end, the back pointer is at position k from the end.

```javascript
function kthFromEnd(head, k) {
  let front = head;
  let back = head;

  // Move front k steps ahead
  for (let i = 0; i < k; i++) {
    if (front === null) return null;  // k is larger than list
    front = front.next;
  }

  // Move both together until front reaches end
  while (front !== null) {
    front = front.next;
    back = back.next;
  }

  return back;
}

const head = arrayToList([1, 2, 3, 4, 5]);
console.log(kthFromEnd(head, 2).val);  // 4
console.log(kthFromEnd(head, 1).val);  // 5 (last element)
```

**Trace:**
```
k=2, list: 1 → 2 → 3 → 4 → 5

Move front 2 steps:
front=1, back=1
front=2, back=1
front=3, back=1

Now move both:
front=4, back=2
front=5, back=3
front=null, back=4 → STOP

back.val = 4 ✓
```

---

## Palindrome Linked List

**Problem:** Check if a linked list is a palindrome.

**Input:** `1 → 2 → 2 → 1` → `true`
**Input:** `1 → 2 → 3` → `false`

**Approach:**
1. Find the middle
2. Reverse the second half
3. Compare first half with reversed second half

```javascript
function isPalindrome(head) {
  if (!head || !head.next) return true;

  // Step 1: find middle
  let slow = head;
  let fast = head;
  while (fast.next && fast.next.next) {
    slow = slow.next;
    fast = fast.next.next;
  }

  // Step 2: reverse second half
  let secondHalf = reverseList(slow.next);

  // Step 3: compare
  let p1 = head;
  let p2 = secondHalf;
  while (p2 !== null) {
    if (p1.val !== p2.val) return false;
    p1 = p1.next;
    p2 = p2.next;
  }

  return true;
}

// Uses reverseList from earlier in this file
const head1 = arrayToList([1, 2, 2, 1]);
console.log(isPalindrome(head1));  // true

const head2 = arrayToList([1, 2, 3]);
console.log(isPalindrome(head2));  // false
```

---

## Summary

| Operation | Technique | Time | Space |
|-----------|-----------|------|-------|
| Reverse entire list | Three pointers (prev, curr, next) | O(n) | O(1) |
| Find middle | Slow + fast pointers | O(n) | O(1) |
| Find kth from end | Two pointers k apart | O(n) | O(1) |
| Check palindrome | Middle + reverse + compare | O(n) | O(1) |

---

## Practice Problems

**Practice 1:** Remove the n-th node from the end of a linked list.
```
Input: 1 → 2 → 3 → 4 → 5, n=2
Output: 1 → 2 → 3 → 5
```

**Practice 2:** Merge two sorted linked lists into one sorted list.
```
Input: 1 → 2 → 4   and   1 → 3 → 4
Output: 1 → 1 → 2 → 3 → 4 → 4
```

---

## Answers

**Practice 1:**
```javascript
function removeNthFromEnd(head, n) {
  const dummy = new ListNode(0);
  dummy.next = head;
  let front = dummy;
  let back = dummy;

  // Move front n+1 steps ahead
  for (let i = 0; i <= n; i++) {
    front = front.next;
  }

  while (front !== null) {
    front = front.next;
    back = back.next;
  }

  back.next = back.next.next;  // skip the nth node from end
  return dummy.next;
}
```

**Practice 2:**
```javascript
function mergeSortedLists(l1, l2) {
  const dummy = new ListNode(0);
  let current = dummy;

  while (l1 !== null && l2 !== null) {
    if (l1.val <= l2.val) {
      current.next = l1;
      l1 = l1.next;
    } else {
      current.next = l2;
      l2 = l2.next;
    }
    current = current.next;
  }

  // Attach remaining nodes
  current.next = l1 !== null ? l1 : l2;

  return dummy.next;
}
```

---

**Next: Day 22 — Linked Lists: Cycle Detection and Review**
How to detect if a linked list has a cycle (forms a loop), and the famous Floyd's algorithm.
