# 03 — Slices, Maps & Strings (the internals)

## 🧠 The One Idea

**Slices, maps, and strings look simple but each is a tiny "header" that secretly points at
shared underlying data — and most Go gotchas come from forgetting that.** A slice isn't an
array; it's a *window* onto one. A map is a *handle* to a hash table. A string is a *read-only
view* of bytes. Once you can picture the little struct behind each, their surprising behaviors
become obvious. *Analogy: a slice is a **bookmark with a length** into a long shelf of books —
copying the bookmark doesn't copy the books.*

---

## 1. Arrays vs slices (start here)

- An **array** has a **fixed size that's part of its type**: `[3]int` and `[4]int` are different
  types. Arrays are **value types** — copying one copies all elements. You rarely use them
  directly.
- A **slice** is a **dynamically-sized, flexible view into an array**. This is what you use 99%
  of the time (`[]int`).

### The slice header (the key mental model)
A slice value is a **3-word struct**:
```
slice = { pointer →  underlying array
          len:       how many elements you can see now
          cap:       how many fit before a regrow is needed }
```
So when you pass a slice to a function, you copy this **little header**, but it still points at
the **same underlying array** — the function can modify your elements.

```go
s := []int{1, 2, 3}     // len 3, cap 3, ptr→[1 2 3]
t := s[0:2]             // t shares the SAME array: len 2, cap 3
t[0] = 99              // s[0] is now 99 too — same backing array!
```

---

## 2. `append` and growth (the famous gotcha)

`append` adds elements. If there's spare **capacity**, it writes in place; if not, it
**allocates a new, bigger array, copies everything over**, and returns a slice pointing at the
new array (roughly doubling capacity for small slices).

```go
s = append(s, 4)   // ALWAYS reassign — append may return a new backing array
```

**The two classic bugs:**
1. **Forgetting to reassign:** `append(s, 4)` without `s =` loses the result if it reallocated.
2. **Aliasing surprise:** two slices sharing one array — `append` to one may or may not affect
   the other depending on whether it had spare cap. *Pre-size with `make([]T, 0, n)` when you
   know the size to avoid repeated regrows and aliasing surprises.*

> **Why this matters:** every regrow is a heap allocation + copy = GC pressure. Pre-allocating
> capacity is the #1 easy Go performance win.

---

## 3. Maps — Go's built-in hash table

- A **map** (`map[K]V`) is an **unordered** key→value store, implemented as a **hash table**.
  The map variable is a **pointer-like handle** to that table, so passing a map around shares
  the same data (changes are seen everywhere).
- **Must be initialized** before writing: `m := make(map[string]int)` (or a literal). A **`nil`
  map** (zero value) can be *read* but **writing to it panics** — a common bug.
- **Reads never fail:** missing key returns the **zero value**. Use the **comma-ok** form to
  tell "absent" from "present-but-zero":
  ```go
  v, ok := m["key"]   // ok == false if the key isn't there
  ```
- **Iteration order is randomized on purpose** — never rely on map order (sort keys if you need
  order).
- **Not safe for concurrent writes** — concurrent map writes **crash** the program. Guard with a
  `sync.Mutex` or use `sync.Map` (Lesson 07). This is a *very* common interview point.

---

## 4. Strings & runes (text, done right)

- A **string** is an **immutable, read-only slice of bytes** (UTF-8 encoded). Its header is just
  `{ pointer → bytes, len }`. Immutable = safe to share freely; you can't change a character in
  place (you build a new string).
- **`len(s)` is the number of BYTES, not characters.** A UTF-8 character can be multiple bytes,
  so `len("héllo")` may be > 5.
- A **rune** is an `int32` = **one Unicode code point** (a "character"). To iterate *characters*,
  `range` a string — it decodes runes for you:
  ```go
  for i, r := range "héllo" {   // r is a rune; i is the BYTE index
      fmt.Printf("%d:%c ", i, r)
  }
  ```
- **Indexing `s[i]` gives a byte**, not a character — a classic non-ASCII bug. Convert with
  `[]rune(s)` for character-wise work, or `[]byte(s)` for raw bytes.
- **Building strings efficiently:** because strings are immutable, `+=` in a loop reallocates
  each time. Use **`strings.Builder`** to append cheaply.

---

## 5. The "shared underlying data" thread (tying it together)

Slices, maps, and strings all carry a **small header that references shared data**:

| Type | Header holds | Copy shares data? | Mutable? |
|---|---|---|---|
| **array** | the elements themselves | no (full copy) | yes |
| **slice** | ptr + len + cap | **yes** (same array) | yes |
| **map** | handle to hash table | **yes** (same table) | yes |
| **string** | ptr + len | **yes** (same bytes) | **no** (immutable) |

So: *passing a slice/map/string is cheap (copies a tiny header), but the callee may see/modify
the same underlying data* — except strings, which are immutable and therefore safe.

---

## 🎤 Say this in the interview

- *"A slice is a **3-word header — pointer, len, cap — over a backing array**, so copies share
  the array. `append` may **reallocate** when it runs out of cap, so I always reassign and
  pre-size with `make` when I can to cut GC pressure."*
- *"Maps are hash tables behind a handle; writing a **nil map panics**, missing keys return the
  zero value (use comma-ok), order is random, and **concurrent writes crash** — I guard with a
  mutex or use `sync.Map`."*
- *"Strings are **immutable UTF-8 byte slices**; `len` counts bytes, a **rune** is a code point,
  and I use `strings.Builder` to build them efficiently."*

➡️ **Next:** [04 — Methods & interfaces (internals)](./04_Methods_And_Interfaces.md)
