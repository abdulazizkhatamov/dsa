# Day 23 — Stacks: What They Are and How to Use Them

---

## What is a Stack?

A stack is a data structure with one rule:

**Last In, First Out (LIFO)**

The last item you PUT in is the first item you GET out.

Think of a stack of plates:
- You add a plate to the TOP
- You take a plate from the TOP
- You can't take from the middle or bottom without removing everything above

```
Push 1, Push 2, Push 3:

      ┌───┐
      │ 3 │  ← top (added last, removed first)
      ├───┤
      │ 2 │
      ├───┤
      │ 1 │
      └───┘

Pop → returns 3
Pop → returns 2
Pop → returns 1
```

---

## Stack in JavaScript

JavaScript doesn't have a built-in Stack class, but an **array** works perfectly:

- `push(val)` → add to the top (end of array)
- `pop()` → remove from the top (end of array)
- `arr[arr.length - 1]` → peek at top without removing

```javascript
const stack = [];

// Push (add to top)
stack.push(1);
stack.push(2);
stack.push(3);
console.log(stack);  // [1, 2, 3]  (3 is the top)

// Peek (look at top without removing)
console.log(stack[stack.length - 1]);  // 3

// Pop (remove from top)
console.log(stack.pop());  // 3
console.log(stack.pop());  // 2
console.log(stack.pop());  // 1
console.log(stack.pop());  // undefined (stack is empty)
```

All operations are O(1).

---

## When is a Stack the Right Tool?

Stacks are useful when:
- You need to **reverse** something
- You need to **undo** an action (go back to previous state)
- You need to match **opening/closing pairs** (brackets, tags)
- You need to track **nested** structures (like function calls)
- The problem involves **"process the most recent thing first"**

---

## Problem 1: Valid Parentheses (Classic)

You already saw this in Day 17. This is the stack's most famous use.

**Problem:** Given a string of `(`, `)`, `[`, `]`, `{`, `}`, check if it's valid.

Rules:
- Every opener must have a matching closer
- They must close in the right order — `([)]` is INVALID, `([])` is valid

```javascript
function isValid(s) {
  const stack = [];
  const pairs = { ')': '(', ']': '[', '}': '{' };

  for (const char of s) {
    if ("([{".includes(char)) {
      stack.push(char);  // push opening brackets
    } else {
      // Closing bracket — must match the top of stack
      if (stack.length === 0 || stack[stack.length - 1] !== pairs[char]) {
        return false;
      }
      stack.pop();
    }
  }

  return stack.length === 0;  // true only if all brackets matched
}

console.log(isValid("()[]{}"));   // true
console.log(isValid("([])"));     // true
console.log(isValid("([)]"));     // false
console.log(isValid("{[}"));      // false
console.log(isValid("("));        // false
```

---

## Problem 2: Min Stack

**Problem:** Design a stack that supports `push`, `pop`, `peek`, and also `getMin` — all in O(1).

The challenge: `getMin` must be O(1), not O(n). You can't just scan the whole stack each time.

**Solution:** Use a second stack that tracks the minimum at every state.

```javascript
class MinStack {
  constructor() {
    this.stack = [];
    this.minStack = [];  // parallel stack: each position holds the current min
  }

  push(val) {
    this.stack.push(val);
    // The new min is the smaller of: current val, or previous min
    const currentMin = this.minStack.length === 0
      ? val
      : Math.min(val, this.minStack[this.minStack.length - 1]);
    this.minStack.push(currentMin);
  }

  pop() {
    this.stack.pop();
    this.minStack.pop();
  }

  peek() {
    return this.stack[this.stack.length - 1];
  }

  getMin() {
    return this.minStack[this.minStack.length - 1];
  }
}

const ms = new MinStack();
ms.push(5);
ms.push(3);
ms.push(7);
ms.push(2);

console.log(ms.getMin());  // 2
ms.pop();
console.log(ms.getMin());  // 3
ms.pop();
console.log(ms.getMin());  // 3
ms.pop();
console.log(ms.getMin());  // 5
```

**Trace through pushes:**
```
push(5): stack=[5], minStack=[5]  (min is 5)
push(3): stack=[5,3], minStack=[5,3]  (min is 3)
push(7): stack=[5,3,7], minStack=[5,3,3]  (min still 3)
push(2): stack=[5,3,7,2], minStack=[5,3,3,2]  (min is 2)

getMin() → minStack top = 2 ✓
pop(): stack=[5,3,7], minStack=[5,3,3]
getMin() → 3 ✓
```

---

## Problem 3: Daily Temperatures

**Problem:** Given an array of temperatures, for each day find how many days until a warmer temperature. If no warmer day comes, use 0.

**Input:** `[73, 74, 75, 71, 69, 72, 76, 73]`
**Output:** `[1, 1, 4, 2, 1, 1, 0, 0]`

Explanation:
- Day 0 (73°): wait 1 day → Day 1 is 74° (warmer)
- Day 2 (75°): wait 4 days → Day 6 is 76° (warmer)
- Day 6 (76°): no warmer day → 0

**Technique: Monotonic Stack** — a stack that stays in sorted order.

The stack stores **indexes** of days we haven't found a warmer temperature for yet. When we find a warmer day, we pop all the cooler days off the stack and calculate their wait time.

```javascript
function dailyTemperatures(temps) {
  const result = new Array(temps.length).fill(0);
  const stack = [];  // stores indexes of days waiting for a warmer day

  for (let i = 0; i < temps.length; i++) {
    // While current day is warmer than the day at the top of stack
    while (stack.length > 0 && temps[i] > temps[stack[stack.length - 1]]) {
      const prevDay = stack.pop();
      result[prevDay] = i - prevDay;  // days waited = current index - previous index
    }
    stack.push(i);  // this day is still waiting for a warmer day
  }

  return result;
}

console.log(dailyTemperatures([73, 74, 75, 71, 69, 72, 76, 73]));
// [1, 1, 4, 2, 1, 1, 0, 0]
```

**Trace (simplified):**
```
i=0, temp=73: stack=[], push 0. stack=[0]
i=1, temp=74: 74>temps[0]=73? Yes! pop 0. result[0]=1-0=1. stack=[1]
i=2, temp=75: 75>temps[1]=74? Yes! pop 1. result[1]=2-1=1. stack=[2]
i=3, temp=71: 71>temps[2]=75? No. push 3. stack=[2,3]
i=4, temp=69: 69>temps[3]=71? No. push 4. stack=[2,3,4]
i=5, temp=72: 72>temps[4]=69? Yes! pop 4. result[4]=5-4=1.
              72>temps[3]=71? Yes! pop 3. result[3]=5-3=2.
              72>temps[2]=75? No. push 5. stack=[2,5]
i=6, temp=76: 76>temps[5]=72? Yes! pop 5. result[5]=6-5=1.
              76>temps[2]=75? Yes! pop 2. result[2]=6-2=4.
              stack empty. push 6. stack=[6]
i=7, temp=73: 73>temps[6]=76? No. push 7. stack=[6,7]

Remaining in stack: [6,7] → result stays 0

Result: [1,1,4,2,1,1,0,0] ✓
```

- Time: O(n) — each index pushed and popped at most once
- Space: O(n) — stack

---

## Summary

| Operation | Time | How |
|-----------|------|-----|
| Push | O(1) | `arr.push(val)` |
| Pop | O(1) | `arr.pop()` |
| Peek | O(1) | `arr[arr.length - 1]` |
| isEmpty | O(1) | `arr.length === 0` |

**Core patterns:**
- **Bracket matching:** push openers, check closers against stack top
- **Min/Max stack:** parallel stack tracking min/max at each state
- **Monotonic stack:** maintain sorted order; pop when violated

---

## Practice Problems

**Practice 1:** Evaluate a simple expression like `"3 4 + 2 *"` (Reverse Polish Notation). Numbers and operators are space-separated. Operators apply to the top two numbers.
```
"3 4 +" → 7  (3 + 4)
"3 4 + 2 *" → 14  ((3+4) * 2)
"5 1 2 + 4 * + 3 -" → 14
```

**Practice 2:** Given a string with letters and `#` characters (where `#` = backspace), simulate typing and return the final string.
```
Input: "ab#c"  → "ac"  ('b' was deleted)
Input: "ab##"  → ""    (both deleted)
Input: "a##b"  → "b"   (a deleted twice, but only one existed)
```

---

## Answers

**Practice 1:**
```javascript
function evalRPN(tokens) {
  const stack = [];

  for (const token of tokens.split(" ")) {
    if (["+", "-", "*", "/"].includes(token)) {
      const b = stack.pop();  // second operand
      const a = stack.pop();  // first operand
      if (token === "+") stack.push(a + b);
      if (token === "-") stack.push(a - b);
      if (token === "*") stack.push(a * b);
      if (token === "/") stack.push(Math.trunc(a / b));  // truncate toward zero
    } else {
      stack.push(Number(token));
    }
  }

  return stack[0];
}

console.log(evalRPN("3 4 + 2 *"));         // 14
console.log(evalRPN("5 1 2 + 4 * + 3 -")); // 14
```

**Practice 2:**
```javascript
function backspaceCompare(s) {
  const stack = [];
  for (const char of s) {
    if (char === "#") {
      stack.pop();  // delete (does nothing if empty)
    } else {
      stack.push(char);
    }
  }
  return stack.join("");
}

console.log(backspaceCompare("ab#c"));   // "ac"
console.log(backspaceCompare("ab##"));  // ""
console.log(backspaceCompare("a##b"));  // "b"
```

---

**Next: Day 24 — Queues: What They Are and How to Use Them**
A queue is the opposite of a stack — First In, First Out (FIFO). Like a real queue at a store: the first person who arrived gets served first.
