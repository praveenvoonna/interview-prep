# 02 — Hashing

## 🧠 The One Idea

**A hash map is a coat-check: hand over a key, get back exactly where your item is — instantly.**
Instead of scanning a list to find something (O(n)), a hash turns the key into an array index so
lookup, insert and delete are **O(1) on average**. Most "make this faster" interview problems are
solved by **remembering what you've already seen** in a map or set.

The one-liner: **"Hashing trades space for time: O(1) average lookup by turning keys into array
indices."**

---

## 1. How a hash map works

- A **hash function** maps a key to a bucket index. Good hashes spread keys evenly.
- **Collisions** (two keys → same bucket) are handled by chaining or open addressing.
- Average O(1); **worst case O(n)** if everything collides (rare with good hashing).
- **Unordered** — don't rely on iteration order (in Go, map order is deliberately randomised).

---

## 2. Go maps & sets

```go
m := map[string]int{}     // map
m["a"]++                  // zero value (0) then +1 — handy for counting
v, ok := m["a"]           // comma-ok: ok=false if absent
delete(m, "a")

set := map[int]struct{}{} // a set: struct{} uses zero memory
set[5] = struct{}{}
_, exists := set[5]
```

`struct{}` as the value type is the idiomatic zero-byte set.

---

## 3. The "seen before" pattern

The single most common hashing trick — one pass, remember as you go:

```go
// Two-sum (UNSORTED): return indices of two numbers adding to target.
func twoSum(nums []int, target int) []int {
    seen := map[int]int{} // value -> index
    for i, n := range nums {
        if j, ok := seen[target-n]; ok {
            return []int{j, i}
        }
        seen[n] = i
    }
    return nil
}
```

O(n) time, O(n) space — beats the O(n²) double loop.

---

## 4. Frequency counting

```go
// First non-repeating character.
func firstUniqChar(s string) int {
    freq := map[rune]int{}
    for _, c := range s {
        freq[c]++
    }
    for i, c := range s {
        if freq[c] == 1 {
            return i
        }
    }
    return -1
}
```

Frequency maps power anagrams, "top-K" (with a heap, Lesson 04), and dedup problems.

---

## 5. Grouping with a derived key

Map a **computed signature** to a bucket — e.g. group anagrams by their sorted letters:

```go
key := sortedLetters(word)        // "eat","tea" -> "aet"
groups[key] = append(groups[key], word)
```

This "key by an invariant" idea appears constantly.

---

## 🎤 Say this in the interview

- *"I'll use a hash map for O(1) average lookups — trading O(n) space to drop this from O(n²) to
  O(n)."*
- *"This is a 'have I seen the complement?' problem, so one pass with a value→index map solves it."*
- *"For grouping I key by an invariant — like sorted letters for anagrams — then bucket into a
  map."*

➡️ **Next:** [03 — Stacks & queues](./03_Stacks_And_Queues.md)
