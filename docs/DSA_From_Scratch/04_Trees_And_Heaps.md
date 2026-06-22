# 04 — Trees & Heaps

## 🧠 The One Idea

**A tree is an org chart: one boss at the top, branching down to leaves.** It lets you store data
so that searching prunes half the work at each step. A **heap** is a special tree that always
keeps the **biggest (or smallest) at the top** — perfect for "give me the top item" repeatedly,
like a priority queue.

The one-liner: **"Binary trees give O(log n) search when balanced; a heap always surfaces the
min/max in O(log n)."**

---

## 1. Binary trees & BSTs

- **Binary tree:** each node has ≤ 2 children.
- **Binary Search Tree (BST):** left subtree < node < right subtree. Search/insert/delete are
  O(height) → **O(log n) if balanced**, O(n) if degenerate (a sorted insert makes a "linked list").
- **Balanced trees** (AVL, red-black) keep height ~log n; Go's `map` is a hash, but ordered
  structures elsewhere often use balanced/B-trees.

---

## 2. Traversals (know all four)

```go
// In-order (Left, Node, Right) — visits a BST in SORTED order.
func inorder(n *TreeNode, out *[]int) {
    if n == nil { return }
    inorder(n.Left, out)
    *out = append(*out, n.Val)
    inorder(n.Right, out)
}
```

- **In-order:** sorted output for a BST.
- **Pre-order** (N,L,R): copy/serialize a tree.
- **Post-order** (L,R,N): delete/free, or compute from children up.
- **Level-order:** BFS with a queue (Lesson 03) — visit depth by depth.

---

## 3. Heaps & priority queues

- A **binary heap** is a complete tree where each parent ≤ (min-heap) or ≥ (max-heap) its
  children. Stored as an array; parent of `i` is `(i-1)/2`.
- `push`/`pop` are **O(log n)**; peek-min/max is **O(1)**.
- Use it whenever you need "**repeatedly take the smallest/largest**": Dijkstra, merge-K-lists,
  scheduling, top-K.

---

## 4. Go's container/heap

```go
import "container/heap"

type MinHeap []int
func (h MinHeap) Len() int            { return len(h) }
func (h MinHeap) Less(i, j int) bool  { return h[i] < h[j] }
func (h MinHeap) Swap(i, j int)       { h[i], h[j] = h[j], h[i] }
func (h *MinHeap) Push(x any)         { *h = append(*h, x.(int)) }
func (h *MinHeap) Pop() any {
    old := *h; n := len(old); x := old[n-1]; *h = old[:n-1]; return x
}
// use: h := &MinHeap{}; heap.Init(h); heap.Push(h, 3); heap.Pop(h)
```

Memorise this skeleton — it appears in many interview solutions.

---

## 5. Top-K pattern (heap)

To find the **K largest** elements: keep a **min-heap of size K**. For each element, push; if size
> K, pop the smallest. End state = the K largest. **O(n log K)** — better than sorting all
(O(n log n)) when K ≪ n.

---

## 🎤 Say this in the interview

- *"BST search is O(log n) when balanced but degrades to O(n) if it's skewed — balanced trees keep
  height logarithmic."*
- *"In-order traversal of a BST yields sorted order; level-order is just BFS with a queue."*
- *"For top-K I keep a size-K min-heap: O(n log K), cheaper than sorting everything when K is
  small."*

➡️ **Next:** [05 — Graphs: BFS & DFS](./05_Graphs_BFS_DFS.md)
