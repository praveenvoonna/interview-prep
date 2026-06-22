# 09 — Interview Q&A Flashcards

Rapid-fire. Cover the answer, say it aloud, then check. Read twice.

---

## Complexity

**Q: What is Big-O?**
Growth rate of cost as n grows, ignoring constants and lower-order terms. Quote worst case.

**Q: Nested vs sequential loops?**
Nested multiply (O(n²)); sequential add (O(n)). Halving each step is O(log n).

**Q: Time vs space trade-off example?**
A hash set: O(n) extra space to get O(1) average lookups.

---

## Arrays & strings

**Q: When two-pointer vs sliding window?**
Two-pointer for sorted/both-ends pair problems; sliding window for contiguous subarray/substring
("at most k").

**Q: Detect a linked-list cycle?**
Fast & slow pointers (Floyd's); they meet inside a cycle. O(1) space.

---

## Hashing

**Q: Hash map complexity and caveat?**
O(1) average, O(n) worst (collisions); unordered — don't rely on iteration order.

**Q: Two-sum on unsorted input?**
One pass with a value→index map: check for the complement before inserting. O(n)/O(n).

---

## Stacks & queues

**Q: Stack vs queue use cases?**
Stack (LIFO): brackets, next-greater, DFS. Queue (FIFO): BFS, level-order, scheduling.

**Q: Monotonic stack — why O(n)?**
Each element is pushed and popped at most once.

---

## Trees & heaps

**Q: BST search complexity?**
O(log n) balanced, O(n) degenerate.

**Q: Which traversal sorts a BST?**
In-order (Left, Node, Right).

**Q: Top-K efficiently?**
Size-K min-heap, O(n log K).

**Q: Heap push/pop/peek costs?**
Push/pop O(log n), peek O(1).

---

## Graphs

**Q: BFS vs DFS?**
BFS (queue): shortest path on unweighted graphs, level by level. DFS (stack/recursion):
connectivity, cycles, topo-sort.

**Q: Weighted shortest path?**
Dijkstra = BFS with a min-heap.

**Q: What's always required in traversal?**
A visited set — or you loop forever on cycles.

---

## Sorting & searching

**Q: Merge vs quicksort?**
Both O(n log n) avg; merge stable + O(n) space, quicksort in-place but O(n²) worst with bad pivots.

**Q: Binary search invariant & overflow-safe mid?**
Answer stays in `[lo,hi]`; `mid = lo + (hi-lo)/2`.

**Q: "Min X such that condition holds"?**
Binary search on the answer space with a monotonic `feasible(x)`.

---

## Dynamic programming

**Q: When is it DP?**
Overlapping sub-problems + optimal substructure.

**Q: The recipe?**
Define state, transition, base case, order (top-down/bottom-up), and which cell is the answer.

**Q: Memoisation vs tabulation?**
Top-down caches recursive calls; bottom-up fills a table iteratively (often space-optimisable).

---

## Go specifics

**Q: Slice aliasing trap?**
`b := a[:2]; append(b,…)` can overwrite `a`'s backing array — `copy` for independence.

**Q: Map iteration order?**
Randomised; sort keys for determinism.

**Q: Efficient string building?**
`strings.Builder`, not `+=` in a loop (O(n²)).

---

🎉 **You finished DSA From Scratch.** The meta-skill that fixes coding-round rejections: **narrate
the flow** — restate, brute-force + Big-O, optimise + Big-O, code, test. Pattern recognition +
clear communication beats memorising solutions.
