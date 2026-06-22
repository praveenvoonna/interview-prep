# 05 — Graphs: BFS & DFS

## 🧠 The One Idea

**A graph is a map of cities and roads; BFS explores like ripples in a pond, DFS like following
one road to its end before backtracking.** Most "is there a path / shortest path / connected
groups" problems are a graph traversal in disguise. The only real choices are **how you store the
graph** and **whether you fan out (BFS) or dive deep (DFS)**.

The one-liner: **"BFS = shortest path on unweighted graphs (queue); DFS = explore deep
(stack/recursion); both need a visited set."**

---

## 1. Representing a graph

```go
// Adjacency list — the default for interviews.
adj := map[int][]int{}
adj[u] = append(adj[u], v) // edge u->v (add reverse too if undirected)
```

- **Adjacency list:** O(V+E) space, great for sparse graphs (most real ones).
- **Adjacency matrix:** O(V²) space, O(1) edge lookup — only for dense graphs.

---

## 2. BFS — shortest path on unweighted graphs

```go
func bfs(adj map[int][]int, start int) map[int]int {
    dist := map[int]int{start: 0}
    queue := []int{start}
    for len(queue) > 0 {
        u := queue[0]; queue = queue[1:]
        for _, v := range adj[u] {
            if _, seen := dist[v]; !seen {
                dist[v] = dist[u] + 1
                queue = append(queue, v)
            }
        }
    }
    return dist
}
```

Because BFS expands in rings, the **first time** you reach a node is the **shortest** (fewest
edges). O(V+E).

---

## 3. DFS — go deep, then backtrack

```go
func dfs(adj map[int][]int, u int, seen map[int]bool) {
    seen[u] = true
    for _, v := range adj[u] {
        if !seen[v] {
            dfs(adj, v, seen)
        }
    }
}
```

Use DFS for **connectivity**, **cycle detection**, **topological sort**, and grid flood-fill.
Watch recursion depth — convert to an explicit stack if the graph is huge.

---

## 4. The grid-as-graph trick

Many problems ("number of islands", "shortest path in a maze") are grids where each cell connects
to its 4 neighbours:

```go
dirs := [][2]int{{0,1},{0,-1},{1,0},{-1,0}}
// for each (r,c), visit (r+dr, c+dc) if in bounds and not visited
```

"Count connected components" = run BFS/DFS from every unvisited land cell; each launch = one
island.

---

## 5. Choosing & the must-haves

| Need | Use |
|---|---|
| shortest path, unweighted | **BFS** |
| connectivity / components / cycles / topo-sort | **DFS** |
| shortest path, **weighted** | **Dijkstra** (BFS + min-heap, Lesson 04) |

**Never forget the visited set** — without it you loop forever on cycles. State it explicitly.

---

## 🎤 Say this in the interview

- *"I'll model this as a graph with an adjacency list. Since edges are unweighted, BFS from the
  source gives shortest paths in O(V+E)."*
- *"For counting islands I run DFS/BFS from each unvisited land cell — each launch is one
  component."*
- *"Both traversals need a visited set to handle cycles; for weighted shortest paths I'd switch to
  Dijkstra with a min-heap."*

➡️ **Next:** [06 — Sorting & searching](./06_Sorting_And_Searching.md)
