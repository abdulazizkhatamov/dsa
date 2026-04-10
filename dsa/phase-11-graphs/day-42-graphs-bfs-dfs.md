# Day 42 — Graphs: BFS/DFS Problems

---

## Shortest Path (BFS)

BFS is perfect for finding the **shortest path** in an unweighted graph. Since BFS processes nodes level by level (distance 1, then distance 2, etc.), the first time we reach a node is guaranteed to be via the shortest path.

**Problem:** Given a graph, find the shortest path (in number of edges) from source to destination.

```javascript
function shortestPath(graph, source, destination) {
  if (source === destination) return 0;

  const visited = new Set([source]);
  const queue = [[source, 0]];  // [node, distance]

  while (queue.length > 0) {
    const [node, distance] = queue.shift();

    for (const neighbor of graph[node] || []) {
      if (neighbor === destination) return distance + 1;

      if (!visited.has(neighbor)) {
        visited.add(neighbor);
        queue.push([neighbor, distance + 1]);
      }
    }
  }

  return -1;  // no path found
}
```

---

## Grid Problems (BFS/DFS)

Many graph problems come as 2D grids. Each cell is a node. Adjacent cells (up/down/left/right) are neighbors.

### Problem: Number of Islands

**Problem:** Count connected regions of '1's (land) in a 2D grid of '1' (land) and '0' (water).

```
grid = [
  ["1","1","0","0"],
  ["1","1","0","0"],
  ["0","0","1","0"],
  ["0","0","0","1"]
]
Answer: 3 islands
```

```javascript
function numIslands(grid) {
  if (!grid || grid.length === 0) return 0;

  const rows = grid.length;
  const cols = grid[0].length;
  let count = 0;

  function dfs(r, c) {
    // Out of bounds, or water, or already visited
    if (r < 0 || r >= rows || c < 0 || c >= cols) return;
    if (grid[r][c] !== '1') return;

    grid[r][c] = '2';  // mark as visited

    dfs(r + 1, c);  // down
    dfs(r - 1, c);  // up
    dfs(r, c + 1);  // right
    dfs(r, c - 1);  // left
  }

  for (let r = 0; r < rows; r++) {
    for (let c = 0; c < cols; c++) {
      if (grid[r][c] === '1') {
        count++;
        dfs(r, c);  // mark entire island as visited
      }
    }
  }

  return count;
}
```

---

### Problem: Shortest Path in a Grid (BFS)

**Problem:** Find the shortest path from top-left to bottom-right in a grid where 0=open and 1=blocked.

```javascript
function shortestPathGrid(grid) {
  const rows = grid.length;
  const cols = grid[0].length;

  if (grid[0][0] === 1 || grid[rows-1][cols-1] === 1) return -1;

  const directions = [[1,0],[-1,0],[0,1],[0,-1]];
  const visited = Array.from({length: rows}, () => new Array(cols).fill(false));
  visited[0][0] = true;
  const queue = [[0, 0, 1]];  // [row, col, distance]

  while (queue.length > 0) {
    const [r, c, dist] = queue.shift();

    if (r === rows - 1 && c === cols - 1) return dist;

    for (const [dr, dc] of directions) {
      const nr = r + dr;
      const nc = c + dc;

      if (nr >= 0 && nr < rows && nc >= 0 && nc < cols
          && !visited[nr][nc] && grid[nr][nc] === 0) {
        visited[nr][nc] = true;
        queue.push([nr, nc, dist + 1]);
      }
    }
  }

  return -1;
}
```

---

## Cycle Detection

### Undirected Graph: DFS + Parent Tracking

```javascript
function hasCycle(graph, n) {
  const visited = new Set();

  function dfs(node, parent) {
    visited.add(node);

    for (const neighbor of graph[node] || []) {
      if (!visited.has(neighbor)) {
        if (dfs(neighbor, node)) return true;
      } else if (neighbor !== parent) {
        return true;  // found a visited node that isn't our parent = cycle
      }
    }

    return false;
  }

  for (let i = 0; i < n; i++) {
    if (!visited.has(i)) {
      if (dfs(i, -1)) return true;
    }
  }

  return false;
}
```

### Directed Graph: DFS + Recursion Stack

```javascript
function hasCycleDirected(graph, n) {
  const visited = new Set();
  const inStack = new Set();  // nodes in current DFS path

  function dfs(node) {
    visited.add(node);
    inStack.add(node);

    for (const neighbor of graph[node] || []) {
      if (!visited.has(neighbor)) {
        if (dfs(neighbor)) return true;
      } else if (inStack.has(neighbor)) {
        return true;  // back edge = cycle
      }
    }

    inStack.delete(node);  // done with this node, remove from stack
    return false;
  }

  for (let i = 0; i < n; i++) {
    if (!visited.has(i)) {
      if (dfs(i)) return true;
    }
  }

  return false;
}
```

---

## Topological Sort

**Problem:** Order tasks such that all dependencies come first. Only valid for directed acyclic graphs (DAGs — no cycles).

**Example:** Course prerequisites. To take course 3, you must first take course 1 and 2.

```javascript
function topologicalSort(n, prerequisites) {
  // prerequisites: [[course, prerequisite], ...]
  const graph = Array.from({length: n}, () => []);
  const inDegree = new Array(n).fill(0);  // how many prerequisites each course has

  for (const [course, prereq] of prerequisites) {
    graph[prereq].push(course);
    inDegree[course]++;
  }

  // Start with courses that have no prerequisites
  const queue = [];
  for (let i = 0; i < n; i++) {
    if (inDegree[i] === 0) queue.push(i);
  }

  const order = [];
  while (queue.length > 0) {
    const course = queue.shift();
    order.push(course);

    for (const next of graph[course]) {
      inDegree[next]--;
      if (inDegree[next] === 0) queue.push(next);  // all prerequisites done
    }
  }

  return order.length === n ? order : [];  // empty = cycle detected
}

// Can you finish all courses? (cycle detection via topological sort)
function canFinish(n, prerequisites) {
  return topologicalSort(n, prerequisites).length === n;
}

console.log(canFinish(2, [[1,0]]));        // true (0→1)
console.log(canFinish(2, [[1,0],[0,1]]));  // false (cycle!)
```

---

## Summary

| Problem | Algorithm | Key Idea |
|---------|-----------|----------|
| Shortest path (unweighted) | BFS | First time you reach a node = shortest distance |
| Reachability | DFS or BFS | Follow neighbors, mark visited |
| Connected components | DFS/BFS from each unvisited | Count how many times you start fresh |
| Cycle detection (undirected) | DFS + parent | Visited + not parent = cycle |
| Cycle detection (directed) | DFS + recursion stack | Back edge = cycle |
| Topological order | BFS (Kahn's) | Process nodes with 0 in-degree first |
| Island problems | DFS on grid | Each cell = node, 4 neighbors = edges |

---

**Next: Day 43 — Graphs: Review + Union-Find**
Review of all graph concepts and introduction to Union-Find — an efficient data structure for tracking connected components.
