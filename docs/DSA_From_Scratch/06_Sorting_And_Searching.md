# 06 — Sorting & Searching

## 🧠 The One Idea

**Sorting is putting a deck of cards in order; binary search is finding a name in a sorted phone
book by repeatedly opening the middle.** You rarely write a sort in an interview (you call the
library), but you must **explain how the good ones work** and **wield binary search**, because
"the data is sorted" almost always means "use binary search for O(log n)".

The one-liner: **"Good sorts are O(n log n); binary search is O(log n) on sorted data — and many
problems hide a binary search."**

---

## 1. The sorts to be able to explain

| Sort | Time (avg) | Space | Stable? | Idea |
|---|---|---|---|---|
| Merge sort | O(n log n) | O(n) | yes | split in half, sort, merge |
| Quicksort | O(n log n)¹ | O(log n) | no | partition around a pivot |
| Heap sort | O(n log n) | O(1) | no | build a heap, pop repeatedly |
| Insertion | O(n²) | O(1) | yes | great for tiny/nearly-sorted |

¹ Quicksort worst case O(n²) with bad pivots; mitigated by randomised/median pivots.

- **Merge sort** = the safe O(n log n) you describe via recurrence `T(n)=2T(n/2)+O(n)`.
- **Quicksort** = faster in practice (cache-friendly, in-place) but needs good pivots.

---

## 2. Sorting in Go

```go
import "sort"
sort.Ints(a)                                  // ascending ints
sort.Slice(p, func(i, j int) bool {           // custom comparator
    return p[i].Age < p[j].Age
})
```

`sort.Slice` is the workhorse. For stability use `sort.SliceStable`. Sorting first often unlocks a
two-pointer or greedy O(n) follow-up.

---

## 3. Binary search — the template

```go
func binarySearch(a []int, target int) int {
    lo, hi := 0, len(a)-1
    for lo <= hi {
        mid := lo + (hi-lo)/2 // avoids overflow
        switch {
        case a[mid] == target:
            return mid
        case a[mid] < target:
            lo = mid + 1
        default:
            hi = mid - 1
        }
    }
    return -1
}
```

Memorise the invariant: the answer is always within `[lo, hi]`. Off-by-one here is the #1 bug —
say the invariant out loud.

---

## 4. Binary search on the answer

The advanced move: when the prompt says "minimum/maximum X such that a condition holds", binary
search **over the answer space**, not the array:

- "Min ship capacity to deliver in D days", "min eating speed (Koko bananas)".
- Define a monotonic `feasible(x)`; binary search the smallest `x` where it's true. O(n log range).

---

## 5. Go's standard binary search

```go
import "sort"
i := sort.SearchInts(a, target)        // leftmost index where a[i] >= target
i = sort.Search(n, func(i int) bool {  // generic predicate form
    return a[i] >= target
})
```

`sort.Search` is exactly the "binary search on a predicate" tool.

---

## 🎤 Say this in the interview

- *"I'll sort with `sort.Slice` (O(n log n)); merge sort is the stable O(n log n) baseline,
  quicksort is faster in practice but worst-case O(n²) without good pivots."*
- *"The array is sorted, so binary search gives O(log n) — I keep the answer inside `[lo,hi]` and
  use `mid = lo+(hi-lo)/2` to avoid overflow."*
- *"This asks for the minimum value satisfying a monotonic condition, so I'll binary-search the
  answer space with a `feasible(x)` predicate."*

➡️ **Next:** [07 — Dynamic programming](./07_Dynamic_Programming.md)
