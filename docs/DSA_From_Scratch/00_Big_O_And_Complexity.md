# 00 — Big-O & Complexity

## 🧠 The One Idea

**Big-O is how an algorithm's cost grows as the input grows — not how fast it is today.** Think of
it as asking "if my data gets 10× bigger, does my code get 10× slower, 100× slower, or barely
notice?" Constants and your laptop's speed don't matter; the **shape of the growth curve** does.

The one-liner: **"Big-O describes growth rate as n → ∞, ignoring constants and lower-order
terms."**

---

## 1. The ladder (memorise this order)

From best to worst, for input size *n*:

| Big-O | Name | Example |
|---|---|---|
| O(1) | constant | hash lookup, array index |
| O(log n) | logarithmic | binary search |
| O(n) | linear | one pass over an array |
| O(n log n) | linearithmic | good sorts (merge/quick) |
| O(n²) | quadratic | nested loops over the same array |
| O(2ⁿ) | exponential | naive recursion (subsets) |
| O(n!) | factorial | permutations |

If you can place your solution on this ladder and justify it, you've answered the complexity
question.

---

## 2. Time vs space complexity

- **Time** = number of operations as n grows.
- **Space** = *extra* memory you allocate (not the input itself), including the **recursion call
  stack**.
- The classic trade-off: a hash set costs O(n) space to buy O(1) lookups — **spend memory to save
  time** (Lesson 02).

---

## 3. How to compute it fast

- **Drop constants:** O(2n) → O(n); O(n/2) → O(n).
- **Drop lower-order terms:** O(n² + n) → O(n²).
- **Sequential loops add:** O(n) + O(n) = O(n).
- **Nested loops multiply:** loop n inside loop n = O(n²).
- **Halving each step = log:** binary search, balanced-tree height.
- **Recursion:** `T(n)=2T(n/2)+O(n)` → O(n log n) (merge sort); subsets → O(2ⁿ).

---

## 4. Best / average / worst

- Quote the **worst case** by default (what interviewers mean).
- Mention **average** when it differs meaningfully: hash lookup is O(1) average but O(n) worst
  (collisions); quicksort is O(n log n) average, O(n²) worst.
- **Amortised** = average over a sequence: appending to a dynamic array is amortised O(1) even
  though an occasional resize is O(n).

---

## 5. The interview habit

Always say, before coding: **"Brute force is O(?). I can do better with [pattern] for O(?) time
and O(?) space."** That single sentence signals seniority and frames the whole solution.

```
Restate problem → brute force + its Big-O → optimised approach + its Big-O → code → test
```

---

## 🎤 Say this in the interview

- *"Big-O is the growth rate as n grows, ignoring constants — I care about the curve's shape, not
  wall-clock time."*
- *"Nested loops multiply, sequential loops add, halving is logarithmic — so this is O(n log n)
  time and O(n) space."*
- *"Hashing trades O(n) space for O(1) average lookups; I'll quote worst case unless average
  differs meaningfully."*

➡️ **Next:** [01 — Arrays, strings & two-pointer](./01_Arrays_Strings_Two_Pointer.md)
