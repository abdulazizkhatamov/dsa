# Day 41 — Graphs: Introduction

---

## What is a Graph?

A graph is a set of **nodes** (also called vertices) connected by **edges**.

Unlike trees:
- Graphs can have **cycles** (paths that loop back)
- There is no "root" — any node can be the starting point
- Nodes can connect to ANY number of other nodes

Real-world examples:
- Social network: people are nodes, friendships are edges
- Maps: cities are nodes, roads are edges
- Internet: web pages are nodes, links are edges
- Dependencies: packages are nodes, "requires" is an edge

---

## Types of Graphs

**Undirected graph:** edges have no direction. If A connects to B, then B connects to A.
```
A — B — C
|       |
D ——————
```

**Directed graph (digraph):** edges have direction. A → B doesn't mean B → A.
```
A → B → C
↑       ↓
D ←←←←←←
```

**Weighted graph:** edges have a weight/cost (e.g., distance between cities).
```
A —5— B —3— C
      |
      2
      |
      D
```

---

## Representing a Graph in Code

### Adjacency List (most common)

For each node, store the list of nodes it connects to.

```javascript
// Undirected graph:
// 0 — 1, 0 — 2, 1 — 3, 2 — 3

const graph = {
  0: [1, 2],
  1: [0, 3],
  2: [0, 3],
  3: [1, 2]
};

// Or using a Map:
const graphMap = new Map();
graphMap.set(0, [1, 2]);
graphMap.set(1, [0, 3]);
graphMap.set(2, [0, 3]);
graphMap.set(3, [1, 2]);
```

### Building from Edge List

```javascript
function buildGraph(edges) {
  const graph = {};

  for (const [a, b] of edges) {
    if (!graph[a]) graph[a] = [];
    if (!graph[b]) graph[b] = [];
    graph[a].push(b);
    graph[b].push(a);  // remove this line for directed graph
  }

  return graph;
}

const edges = [[0,1],[0,2],[1,3],[2,3]];
const graph = buildGraph(edges);
// {0:[1,2], 1:[0,3], 2:[0,3], 3:[1,2]}
```

---

## DFS on a Graph

DFS on a tree is simple — you just recurse on children. On a graph, you must track **visited** nodes to avoid infinite loops (cycles!).

```javascript
function dfs(graph, start) {
  const visited = new Set();
  const result = [];

  function explore(node) {
    if (visited.has(node)) return;  // already visited — don't revisit
    visited.add(node);
    result.push(node);

    for (const neighbor of graph[node]) {
      explore(neighbor);
    }
  }

  explore(start);
  return result;
}

const graph = {0:[1,2], 1:[0,3], 2:[0,3], 3:[1,2]};
console.log(dfs(graph, 0));  // [0, 1, 3, 2]  (or similar — order depends on neighbors)
```

---

## BFS on a Graph

Same idea — use a queue, mark visited nodes.

```javascript
function bfs(graph, start) {
  const visited = new Set([start]);
  const queue = [start];
  const result = [];

  while (queue.length > 0) {
    const node = queue.shift();
    result.push(node);

    for (const neighbor of graph[node]) {
      if (!visited.has(neighbor)) {
        visited.add(neighbor);
        queue.push(neighbor);
      }
    }
  }

  return result;
}

console.log(bfs(graph, 0));  // [0, 1, 2, 3]  (level by level)
```

---

## Problem 1: Number of Connected Components

**Problem:** Given n nodes (0 to n-1) and a list of edges, count how many connected components there are.

```
n=5, edges=[[0,1],[1,2],[3,4]]

Component 1: 0-1-2
Component 2: 3-4

Answer: 2
```

```javascript
function countComponents(n, edges) {
  const graph = {};
  for (let i = 0; i < n; i++) graph[i] = [];
  for (const [a, b] of edges) {
    graph[a].push(b);
    graph[b].push(a);
  }

  const visited = new Set();
  let components = 0;

  for (let node = 0; node < n; node++) {
    if (!visited.has(node)) {
      // DFS from this node — marks all reachable nodes as visited
      dfsVisit(graph, node, visited);
      components++;  // every new unvisited starting node = new component
    }
  }

  return components;
}

function dfsVisit(graph, node, visited) {
  if (visited.has(node)) return;
  visited.add(node);
  for (const neighbor of graph[node]) {
    dfsVisit(graph, neighbor, visited);
  }
}

console.log(countComponents(5, [[0,1],[1,2],[3,4]]));  // 2
console.log(countComponents(5, [[0,1],[1,2],[2,3],[3,4]]));  // 1
```

---

## Problem 2: Has Path (DFS)

**Problem:** Given a directed graph and two nodes, does a path exist from source to destination?

```javascript
function hasPath(graph, source, destination) {
  if (source === destination) return true;

  const visited = new Set();

  function dfs(node) {
    if (node === destination) return true;
    if (visited.has(node)) return false;
    visited.add(node);

    for (const neighbor of graph[node] || []) {
      if (dfs(neighbor)) return true;
    }

    return false;
  }

  return dfs(source);
}
```

---

## Summary

| Concept | Key Point |
|---------|-----------|
| Node/Vertex | A point in the graph |
| Edge | Connection between two nodes |
| Directed | Edge has a direction |
| Undirected | Edge is bidirectional |
| Cycle | Path that loops back to the start |
| Connected | Every node can reach every other node |
| Adjacency List | `{node: [neighbors]}` — most common representation |
| Visited Set | Required in graphs to avoid infinite loops |

---

**Next: Day 42 — Graphs: BFS/DFS Problems**
Solving classic graph problems: shortest path, cycle detection, and topological sort.
