# Day 22 — Linked Lists: Cycle Detection and Review

---

## What is a Cycle?

A cycle in a linked list means some node's `next` pointer points BACK to an earlier node. The list loops forever — you'd never reach `null`.

```
Normal list:
1 → 2 → 3 → 4 → null

List with cycle:
1 → 2 → 3 → 4
        ↑       |
        └───────┘
(4.next points back to 3 — creates a loop)
```

If you tried to traverse this naively with a `while (current !== null)` loop, you'd loop forever.

---

## Floyd's Cycle Detection Algorithm (The Tortoise and Hare)

This is one of the most elegant algorithms in CS. It uses two pointers:
- **Slow** (tortoise): moves 1 step at a time
- **Fast** (hare): moves 2 steps at a time

**Key insight:** If there IS a cycle, the fast pointer will eventually "lap" the slow pointer and they'll meet inside the cycle. If there's NO cycle, the fast pointer will reach `null`.

Think of two runners on a circular track — the faster one will always catch up to the slower one and they'll be at the same spot eventually.

```javascript
function hasCycle(head) {
  let slow = head;
  let fast = head;

  while (fast !== null && fast.next !== null) {
    slow = slow.next;        // 1 step
    fast = fast.next.next;   // 2 steps

    if (slow === fast) return true;  // they met — cycle exists!
  }

  return false;  // fast reached null — no cycle
}
```

- Time: O(n)
- Space: O(1) — no extra data structures!

---

## Tracing Through a Cycle

```
List: 1 → 2 → 3 → 4 → 5, where 5.next = 3 (cycle back to node 3)

Positions:
Node 1 at pos 1
Node 2 at pos 2
Node 3 at pos 3
Node 4 at pos 4
Node 5 at pos 5
(then back to pos 3)

Start: slow=1, fast=1

Step 1: slow=2, fast=3
Step 2: slow=3, fast=5
Step 3: slow=4, fast=4  ← fast went 5→3→... no wait:
  slow moves: 4→5 (one step from 4)... hmm let me redo

Let's number nodes: A(1), B(2), C(3), D(4), E(5), then E.next=C

slow=A, fast=A
Step 1: slow=B, fast=C
Step 2: slow=C, fast=E
Step 3: slow=D, fast=D (fast went E→C→D)
  slow === fast! → return true ✓
```

---

## Finding Where the Cycle Starts

**Problem:** If a cycle exists, return the node where the cycle begins.

**Input:** `1 → 2 → 3 → 4 → 5 → (back to 3)`
**Output:** node with value `3`

**The math insight** (you don't need to memorize why this works, just the algorithm):
1. Detect the meeting point using slow/fast
2. Reset one pointer to `head`
3. Move both pointers ONE step at a time
4. Where they meet again = the cycle start

```javascript
function detectCycleStart(head) {
  let slow = head;
  let fast = head;

  // Phase 1: detect if cycle exists
  while (fast !== null && fast.next !== null) {
    slow = slow.next;
    fast = fast.next.next;
    if (slow === fast) break;
  }

  // No cycle
  if (fast === null || fast.next === null) return null;

  // Phase 2: find the cycle start
  slow = head;  // reset slow to head
  while (slow !== fast) {
    slow = slow.next;
    fast = fast.next;  // now both move 1 step
  }

  return slow;  // they meet at the cycle start
}
```

---

## Linked List: Intersection of Two Lists

**Problem:** Given two linked lists that might share a common tail, find the node where they intersect.

```
List A:  4 → 1 ↘
                  8 → 4 → 5 → null
List B:  5 → 6 ↗
```

The intersecting node is `8`.

**Key insight:** Both lists share the SAME nodes from the intersection onward (same references, not just same values).

**Approach using length difference:**
1. Find lengths of both lists
2. Advance the longer list by the difference
3. Walk both lists together until they meet

```javascript
function getIntersectionNode(headA, headB) {
  let a = headA;
  let b = headB;

  // When a reaches the end, redirect to headB
  // When b reaches the end, redirect to headA
  // They'll meet at the intersection (or both reach null at same time)
  while (a !== b) {
    a = a === null ? headB : a.next;
    b = b === null ? headA : b.next;
  }

  return a;  // intersection node (or null if no intersection)
}
```

**Why this works:** If list A has length `a` and list B has length `b`, pointer A travels `a + b` and pointer B travels `b + a` — the same total distance. They'll meet at the intersection.

---

## Phase 5 Review: All Linked List Operations

```
Operation              Time    Space    Technique
---------              ----    -----    ---------
Traverse               O(n)    O(1)     Simple loop
Find length            O(n)    O(1)     Count while traversing
Insert at head         O(1)    O(1)     Reconnect pointers
Insert at tail         O(n)    O(1)     Traverse to end, add
Delete at head         O(1)    O(1)     Move head pointer
Delete specific value  O(n)    O(1)     Traverse and skip
Reverse                O(n)    O(1)     Three pointers (prev, curr, next)
Find middle            O(n)    O(1)     Slow + fast pointers
Find kth from end      O(n)    O(1)     Two pointers k apart
Detect cycle           O(n)    O(1)     Floyd's (slow + fast)
Find cycle start       O(n)    O(1)     Floyd's phase 2
Merge two sorted       O(n+m)  O(1)     Compare heads, build result
Check palindrome       O(n)    O(1)     Middle + reverse + compare
```

---

## Phase 5 Self-Check

- [ ] What is a linked list node made of?
- [ ] Why is insert at head O(1) for linked lists but O(n) for arrays?
- [ ] How do you reverse a linked list? What are the three pointers called?
- [ ] How does the slow/fast pointer trick find the middle?
- [ ] What is Floyd's cycle detection algorithm? How does it work?
- [ ] How do you find the kth node from the end?

---

## The Dummy Node Trick

You'll notice in several solutions we created a "dummy" node before the head. This is a common linked list trick that simplifies code when you might need to modify the head node.

```javascript
const dummy = new ListNode(0);
dummy.next = head;
// ...do operations...
return dummy.next;  // this is the (possibly new) head
```

Without a dummy node, you'd need special cases for "what if I'm removing the first node?" or "what if the result starts differently?" The dummy node gives you a safe "node before the head" to work with.

---

**Next: Day 23 — Stacks: What They Are**
Phase 6 begins. A stack is a simple but powerful data structure with one rule: last in, first out (LIFO). You push items on top and pop them off the top. Used everywhere: function calls, undo/redo, bracket matching, parsing expressions.
