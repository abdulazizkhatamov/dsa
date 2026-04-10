# Day 43 — Graphs: Review + Union-Find

---

## Union-Find (Disjoint Set Union)

Union-Find is a data structure for tracking which elements belong to the same group (component). It supports two operations efficiently:

- **find(x):** which group does x belong to?
- **union(x, y):** merge the groups of x and y

Classic use case: detecting cycles, counting connected components.

```javascript
class UnionFind {
  constructor(n) {
    this.parent = Array.from({length: n}, (_, i) => i);  // each node is its own parent
    this.rank = new Array(n).fill(0);  // for optimization
  }

  find(x) {
    // Path compression: make every node point directly to root
    if (this.parent[x] !== x) {
      this.parent[x] = this.find(this.parent[x]);
    }
    return this.parent[x];
  }

  union(x, y) {
    const rootX = this.find(x);
    const rootY = this.find(y);

    if (rootX === rootY) return false;  // already same component — would create cycle!

    // Union by rank: attach smaller tree under larger tree
    if (this.rank[rootX] < this.rank[rootY]) {
      this.parent[rootX] = rootY;
    } else if (this.rank[rootX] > this.rank[rootY]) {
      this.parent[rootY] = rootX;
    } else {
      this.parent[rootY] = rootX;
      this.rank[rootX]++;
    }

    return true;
  }

  connected(x, y) {
    return this.find(x) === this.find(y);
  }
}
```

**Detecting cycles with Union-Find:**
```javascript
function hasCycleUF(n, edges) {
  const uf = new UnionFind(n);

  for (const [a, b] of edges) {
    if (!uf.union(a, b)) return true;  // union returns false if already connected = cycle
  }

  return false;
}
```

---

## Phase 11 Self-Check

- [ ] What is a graph? How is it different from a tree?
- [ ] What is the difference between directed and undirected?
- [ ] How do you represent a graph with an adjacency list?
- [ ] Why do graphs need a `visited` set but trees usually don't?
- [ ] What is DFS on a graph? What is BFS?
- [ ] Why is BFS used for shortest path?
- [ ] What is topological sort? When can you use it?
- [ ] What is Union-Find? What does `union` and `find` do?

---

## Phase 11 Summary

```
Day 41: Graphs intro — nodes, edges, adjacency list, DFS, BFS
Day 42: Graph problems — shortest path, islands, cycle detection, topological sort
Day 43: Review + Union-Find — efficient connected components
```

---

**Next: Day 44 — Dynamic Programming: Introduction**
Phase 12 begins. Dynamic Programming (DP) is the hardest but most powerful technique in DSA. It solves problems by breaking them into overlapping subproblems and caching the results. Recursion + memoization = DP.
