# 🧩 DSA From Scratch — A Crash Course (in plain English)

A from-zero data-structures-and-algorithms course aimed squarely at **passing coding rounds**.
Patterns first, in **Go**, with the reasoning you can say out loud while solving.

> **How to read this:** go in order, 00 → 09. Each lesson starts with a plain-English analogy
> (**"The One Idea"**), then the real mechanics + a worked pattern, then **"Say this in the
> interview"** lines for explaining your approach and complexity.

> **Status:** 📝 *Syllabus ready — lessons to be written.* This README is the outline and the
> reference you can study from today.

---

## 📂 The lessons (planned, read in order)

### Part A — Foundations
- **00 — Big-O & complexity** — time vs space, how to talk about it without hand-waving.
- **01 — Arrays, strings & two-pointer** — sliding window, in-place tricks.
- **02 — Hashing** — maps/sets, frequency counts, the "seen before" pattern.

### Part B — Linear & hierarchical structures
- **03 — Stacks & queues** — monotonic stack, BFS queue, parsing.
- **04 — Trees & heaps** — binary trees, BST, traversals, priority queues, top-K.

### Part C — Graphs & ordering
- **05 — Graphs: BFS / DFS** — adjacency lists, visited sets, shortest path on unweighted.
- **06 — Sorting & searching** — quicksort/mergesort intuition, binary search variants.

### Part D — The hard part
- **07 — Dynamic programming** — recognise it, define state, memoise → tabulate.

### Part E — Interview craft
- **08 — Go patterns for interviews** — slices/maps idioms, custom sort, `container/heap`,
  reading input fast, avoiding the classic Go gotchas under time pressure.
- **09 — Interview Q&A flashcards** — complexity drills + "how would you approach…" prompts.

---

## 🧠 The whole course in three sentences
1. **Most problems are a known pattern in disguise:** two-pointer, hashing, BFS/DFS, heap, or DP —
   recognising the pattern is 80% of the battle.
2. **State your approach and complexity before coding:** interviewers score the reasoning, not
   just the final code.
3. **In Go, a few idioms cover most rounds:** slices, maps, `sort`, and `container/heap` — know
   them cold so you spend time on the algorithm, not the syntax.

---

## 🎯 How to drill
- Do one **pattern** per session; solve 3–4 problems that use it before moving on.
- Always restate the problem, give brute force → optimised, then code.
- Pair this with **[Go From Scratch](../Golang_From_Scratch/)** for language mechanics.

When the lessons are written, start at **00**.
