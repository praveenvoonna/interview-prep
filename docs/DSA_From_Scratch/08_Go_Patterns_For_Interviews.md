# 08 — Go Patterns for Interviews

## 🧠 The One Idea

**Know a handful of Go idioms so cold that you spend your interview thinking about the algorithm,
not the syntax.** Most coding rounds are won or lost
on fluency: reading input fast, using slices/maps without fumbling, and avoiding Go's few sharp
edges under time pressure.

The one-liner: **"A small toolkit — slices, maps, sort, heap, fast I/O — covers the vast majority
of Go coding rounds."**

---

## 1. Fast input reading

`fmt.Scan` is slow for big inputs. Use a buffered scanner:

```go
import ("bufio"; "os"; "strconv")

func main() {
    sc := bufio.NewScanner(os.Stdin)
    sc.Buffer(make([]byte, 1024*1024), 1024*1024) // big lines
    sc.Split(bufio.ScanWords)
    next := func() int { sc.Scan(); n, _ := strconv.Atoi(sc.Text()); return n }
    w := bufio.NewWriter(os.Stdout); defer w.Flush()
    n := next()
    _ = n
    // ...read with next(), write with fmt.Fprintln(w, ...)
}
```

Buffered read + buffered write avoids TLE on large test cases.

---

## 2. Slice gotchas (the ones that bite)

```go
a := []int{1, 2, 3}
b := a[:2]          // shares the SAME backing array as a!
b = append(b, 99)   // may overwrite a[2] — aliasing bug

c := make([]int, len(a)); copy(c, a) // independent copy

// Closure capture in a loop (pre-Go 1.22): copy the variable
for _, v := range a {
    v := v
    go func() { _ = v }()
}
```

Slices are views over an array — **aliasing** and **append reallocation** are the classic traps.

---

## 3. Maps as the everything-tool

```go
freq := map[int]int{}; freq[x]++         // counting
set := map[int]struct{}{}; set[x] = struct{}{} // set
v, ok := m[k]                            // membership
```

Remember: **map iteration order is randomised** — never rely on it; sort keys if you need order.

---

## 4. Custom sort & heap (have these ready)

```go
sort.Slice(xs, func(i, j int) bool { return xs[i] < xs[j] })

// Min-heap skeleton (full version in Lesson 04):
// implement Len/Less/Swap/Push/Pop, then heap.Init/Push/Pop.
```

`sort.Slice` + the `container/heap` skeleton cover top-K, Dijkstra, scheduling, interval problems.

---

## 5. Strings, runes & numbers

```go
// Iterate bytes vs runes:
for i := 0; i < len(s); i++ { _ = s[i] }   // bytes (ASCII)
for _, r := range s { _ = r }              // runes (unicode)

[]byte(s); string(bs)                       // string <-> bytes
strconv.Atoi("42"); strconv.Itoa(42)        // parse / format
strings.Builder{}                           // efficient concatenation
```

Strings are immutable; build with `strings.Builder`, not `+=` in a loop (O(n²)).

---

## 6. The interview flow (rehearse it)

1. **Restate** the problem + clarify edge cases (empty, negatives, duplicates).
2. **Brute force + Big-O**, then the optimised approach + Big-O (Lesson 00).
3. **Code** cleanly, talking through it.
4. **Test** by hand on a small example + an edge case.
5. **State final complexity.**

Communicating this flow matters as much as the code — it's the difference your rejections point to.

---

## 🎤 Say this in the interview

- *"I'll read input with a buffered scanner and write buffered to avoid timeouts on large cases."*
- *"Careful — `b := a[:2]; append(b,…)` can alias `a`'s backing array; I'll `copy` if I need an
  independent slice."*
- *"Map order is randomised in Go, so if I need deterministic order I'll collect and sort the
  keys."*

➡️ **Next:** [09 — Interview Q&A flashcards](./09_Interview_QA_Flashcards.md)
