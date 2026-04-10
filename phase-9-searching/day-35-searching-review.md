# Day 35 — Searching: Review

---

## The Three Ways to Search

| Method | Requires | Time | When to Use |
|--------|----------|------|-------------|
| Linear Search | Nothing | O(n) | Unsorted data, small datasets |
| Binary Search | Sorted data | O(log n) | Sorted arrays, answer-space problems |
| Hash-based | Pre-processing | O(1) average | Repeated lookups, existence checks |

---

## Linear Search

Simple: check every element until you find what you're looking for.

```javascript
function linearSearch(arr, target) {
  for (let i = 0; i < arr.length; i++) {
    if (arr[i] === target) return i;
  }
  return -1;
}
```

- O(n) time
- O(1) space
- No special requirements — works on ANY data, sorted or unsorted

When is linear search the RIGHT choice?
- Data is unsorted and sorting isn't worth it
- You only search once (sorting to then binary search would be O(n log n) total — worse)
- Small arrays (constant factor overhead of binary search isn't worth it)

---

## Hash-Based Search (O(1) Lookup)

Put everything in a Set or Map first. Then lookups are O(1).

```javascript
// Build once: O(n)
const lookupSet = new Set([1, 5, 9, 14, 20]);

// Each lookup: O(1)
console.log(lookupSet.has(9));   // true
console.log(lookupSet.has(7));   // false
```

Best for: repeated lookups into the same dataset.

---

## Choosing the Right Search

```
Is the data sorted?
├── Yes → Binary Search (O(log n))
│   └── Will you search many times into the same data?
│       └── Consider building a hash map first if O(n) prep is acceptable
└── No  → Is it worth sorting first?
    ├── You'll search once → Linear Search (O(n))
    └── You'll search many times → Sort first O(n log n), then binary O(log n) each
        OR build hash map O(n), then O(1) per lookup
```

---

## Phase 9 Summary

```
Day 33: Binary Search — basic template, variants, off-by-one
Day 34: Binary Search Advanced — 2D matrix, answer space
Day 35: Review — linear search, hash-based, choosing the right method
```

---

**Next: Day 36 — Trees: Introduction**
Phase 10 begins. Trees are hierarchical data structures — like a family tree or file system. The binary tree is the most important type, and it's the foundation for binary search trees, heaps, and many other structures.
