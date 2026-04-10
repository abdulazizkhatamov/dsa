# Day 24 — Queues: What They Are and How to Use Them

---

## What is a Queue?

A queue has one rule:

**First In, First Out (FIFO)**

The first item you put in is the first item you get out.

Think of a line at a coffee shop:
- First person arrives → they're at the FRONT
- New people join at the BACK
- The person at the front gets served first

```
Enqueue (add to back):  ← 3 ← 2 ← 1  → Dequeue (remove from front)
                           back        front

Add 1, Add 2, Add 3:
front [1, 2, 3] back

Dequeue → 1 (first in, first out)
Dequeue → 2
Dequeue → 3
```

Stack = LIFO (last plate on pile comes off first)
Queue = FIFO (first in line gets served first)

---

## Queue in JavaScript

A simple queue using an array:

```javascript
const queue = [];

// Enqueue (add to back)
queue.push(1);
queue.push(2);
queue.push(3);
console.log(queue);  // [1, 2, 3]

// Dequeue (remove from front)
console.log(queue.shift());  // 1 (front)
console.log(queue.shift());  // 2
console.log(queue.shift());  // 3

// Peek at front
console.log(queue[0]);  // undefined (empty now)
```

**Warning:** `shift()` (removing from front of array) is technically O(n) because it shifts all elements. For small queues this is fine. For large-scale queues, use a proper implementation with a head pointer.

---

## More Efficient Queue (with head pointer)

```javascript
class Queue {
  constructor() {
    this.data = {};
    this.head = 0;
    this.tail = 0;
  }

  enqueue(val) {
    this.data[this.tail] = val;
    this.tail++;
  }

  dequeue() {
    if (this.isEmpty()) return undefined;
    const val = this.data[this.head];
    delete this.data[this.head];
    this.head++;
    return val;
  }

  peek() {
    return this.data[this.head];
  }

  isEmpty() {
    return this.head === this.tail;
  }

  get size() {
    return this.tail - this.head;
  }
}

const q = new Queue();
q.enqueue(1);
q.enqueue(2);
q.enqueue(3);
console.log(q.dequeue());  // 1
console.log(q.peek());     // 2
console.log(q.size);       // 2
```

All operations are O(1).

---

## When is a Queue the Right Tool?

Use a queue when:
- You process things in the order they arrive
- You need **level-by-level traversal** (trees, graphs — Phase 10)
- You model real-world queues (tasks, messages, requests)
- The problem says "process in order" or "breadth-first"

---

## Problem 1: Number of Recent Calls

**Problem:** You have a service that gets pinged. Count how many pings happened in the last 3000 milliseconds (inclusive).

```javascript
class RecentCounter {
  constructor() {
    this.queue = [];
  }

  ping(t) {
    this.queue.push(t);

    // Remove pings that are too old (outside the 3000ms window)
    while (this.queue[0] < t - 3000) {
      this.queue.shift();
    }

    return this.queue.length;
  }
}

const rc = new RecentCounter();
console.log(rc.ping(1));     // 1  (only 1 ping in range [−2999, 1])
console.log(rc.ping(100));   // 2  (pings at 1 and 100)
console.log(rc.ping(3001));  // 3  (pings at 1, 100, 3001)
console.log(rc.ping(3002));  // 3  (1 is now too old: 3002-3000=2, but 1 < 2, so removed)
```

The queue naturally holds only pings in the valid window.

---

## Problem 2: Implement a Queue Using Two Stacks

**Problem:** Implement a queue using only two stacks.

This is a classic interview question. The idea:
- `stack1` is for pushing new elements
- `stack2` is for popping (acts as the "reversed" stack)

```javascript
class MyQueue {
  constructor() {
    this.inStack = [];   // for enqueue
    this.outStack = [];  // for dequeue
  }

  enqueue(val) {
    this.inStack.push(val);
  }

  dequeue() {
    // If outStack is empty, pour everything from inStack into it
    if (this.outStack.length === 0) {
      while (this.inStack.length > 0) {
        this.outStack.push(this.inStack.pop());
      }
    }
    return this.outStack.pop();
  }

  peek() {
    if (this.outStack.length === 0) {
      while (this.inStack.length > 0) {
        this.outStack.push(this.inStack.pop());
      }
    }
    return this.outStack[this.outStack.length - 1];
  }

  isEmpty() {
    return this.inStack.length === 0 && this.outStack.length === 0;
  }
}

const q = new MyQueue();
q.enqueue(1);
q.enqueue(2);
q.enqueue(3);
console.log(q.dequeue());  // 1  (first in, first out)
console.log(q.peek());     // 2
console.log(q.dequeue());  // 2
```

**Trace:**
```
enqueue(1): inStack=[1]
enqueue(2): inStack=[1,2]
enqueue(3): inStack=[1,2,3]

dequeue():
  outStack is empty → pour inStack into outStack
  outStack=[3,2,1]  inStack=[]
  pop from outStack → 1 ✓ (first element!)

peek():
  outStack=[3,2]
  top of outStack = 2 ✓
```

- Each element is moved at most twice (once into inStack, once into outStack)
- Amortized O(1) per operation

---

## Problem 3: Sliding Window Maximum

**Problem:** Given an array and a window size `k`, find the maximum value in each window.

**Input:** `arr = [1, 3, -1, -3, 5, 3, 6, 7]`, `k = 3`
**Output:** `[3, 3, 5, 5, 6, 7]`

This uses a **Deque** (double-ended queue) — a queue where you can add/remove from BOTH ends.

In JavaScript, an array can work as a deque:
- `push` = add to back
- `pop` = remove from back
- `shift` = remove from front
- `unshift` = add to front

**Approach: Monotonic Deque**

We keep a deque of indexes. The deque stays in decreasing order of values. When we slide the window:
- Remove elements that are outside the window (from front)
- Remove elements that are smaller than the new element (from back — they can never be the max)
- The front of the deque is always the current window's maximum

```javascript
function maxSlidingWindow(arr, k) {
  const result = [];
  const deque = [];  // stores indexes, values decrease from front to back

  for (let i = 0; i < arr.length; i++) {
    // Remove elements outside the window (front of deque)
    while (deque.length > 0 && deque[0] < i - k + 1) {
      deque.shift();
    }

    // Remove elements smaller than current (from back — they're useless)
    while (deque.length > 0 && arr[deque[deque.length - 1]] < arr[i]) {
      deque.pop();
    }

    deque.push(i);  // add current index

    // Start adding to result once we have a full window
    if (i >= k - 1) {
      result.push(arr[deque[0]]);  // front of deque = max of current window
    }
  }

  return result;
}

console.log(maxSlidingWindow([1, 3, -1, -3, 5, 3, 6, 7], 3));
// [3, 3, 5, 5, 6, 7]
```

- Time: O(n) — each element added and removed at most once
- Space: O(k) — deque holds at most k elements

---

## Stack vs Queue: Quick Comparison

| Feature | Stack | Queue |
|---------|-------|-------|
| Order | LIFO (last in, first out) | FIFO (first in, first out) |
| Add | Push to top | Enqueue to back |
| Remove | Pop from top | Dequeue from front |
| Real-world analogy | Stack of plates | Line at a store |
| Use in DSA | Bracket matching, undo, DFS | BFS, level-order traversal, scheduling |

---

## Summary

Both stacks and queues are just arrays with a specific discipline:
- Stack: only touch the end (push/pop)
- Queue: add to end, remove from front (enqueue/dequeue)

Their power comes from this restriction — it forces a specific order that matches real-world or algorithmic needs.

---

## Practice Problems

**Practice 1:** Given a queue, reverse its order using only a stack.
```
Queue before: front [1, 2, 3, 4] back
Queue after:  front [4, 3, 2, 1] back
```

**Practice 2:** Implement a Stack using two Queues.
(The reverse of Problem 2 above — only use queue operations.)

---

## Answers

**Practice 1:**
```javascript
function reverseQueue(queue) {
  const stack = [];

  // Dequeue everything into stack
  while (queue.length > 0) {
    stack.push(queue.shift());
  }

  // Pop everything back into queue (now reversed)
  while (stack.length > 0) {
    queue.push(stack.pop());
  }

  return queue;
}

let q = [1, 2, 3, 4];
console.log(reverseQueue(q));  // [4, 3, 2, 1]
```

**Practice 2:**
```javascript
class StackUsingQueues {
  constructor() {
    this.q1 = [];
    this.q2 = [];
  }

  push(val) {
    // Add to q2, then pour q1 into q2, then swap
    this.q2.push(val);
    while (this.q1.length > 0) {
      this.q2.push(this.q1.shift());
    }
    [this.q1, this.q2] = [this.q2, this.q1];
  }

  pop() {
    return this.q1.shift();
  }

  peek() {
    return this.q1[0];
  }

  isEmpty() {
    return this.q1.length === 0;
  }
}
```

---

**Next: Day 25 — Stacks & Queues: Mixed Problems and Review**
We'll combine stack and queue patterns and solve problems that require knowing which to use.
