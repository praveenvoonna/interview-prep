# 08 — Memory & the Garbage Collector

## 🧠 The One Idea

**Go cleans up memory for you, but it does it *while your program keeps running*, so the pauses
are tiny.** In C you manually `malloc`/`free` (and leak or crash if you get it wrong). In Java
the old GC could "stop the world" for noticeable pauses. Go's garbage collector is designed to
do almost all its work **concurrently** with your code, targeting **sub-millisecond** pauses —
giving you memory safety **and** predictable latency. *Analogy: instead of closing the whole
restaurant to clean (stop-the-world), Go's busboys clear tables while diners keep eating.*

---

## 1. Why a GC at all?

- **Manual memory (C/C++):** you allocate and must remember to free. Forget → **leak**; free
  twice or too early → **crash / use-after-free / security hole**. Powerful but dangerous.
- **Garbage collection (Go/Java/Python):** the runtime **automatically reclaims** memory that's
  no longer reachable. You can't double-free or use-after-free. **Safety + productivity** at the
  cost of some runtime work.
- Go's stance: pay a **small, well-controlled** GC cost to get safety and simplicity — the right
  trade for servers and tools (just not hard-real-time embedded).

---

## 2. Recap: stack vs heap (where the GC even applies)

(From Lesson 02 — it's the foundation.)
- **Stack** memory (local variables that don't escape) is freed **automatically and instantly**
  when a function returns. **The GC never touches the stack.** Cheap.
- **Heap** memory (values that **escape** — outlive their function) is what the **GC manages**.
- So **the less you put on the heap, the less GC work** — which is why escape analysis matters.

### Escape analysis (the compiler's job)
The compiler decides at build time whether each value can stay on the stack or must "escape" to
the heap (e.g., its address is returned or stored somewhere longer-lived).

```bash
go build -gcflags=-m main.go    # prints "escapes to heap" / "moved to heap" decisions
```

Common escape causes: returning `&local`, storing pointers in a slice/map/struct that outlives
the call, passing to `interface{}`/`fmt.Println` (boxing), large objects. **Reducing needless
heap escapes = the main way Go devs cut GC pressure.**

---

## 3. How Go's GC works (the internal, in plain terms)

Go uses a **concurrent, tri-color, mark-and-sweep** collector. Unpack the jargon:

- **Mark-and-sweep** = two phases: **mark** everything still reachable (starting from "roots" —
  globals, stacks, registers — and following pointers), then **sweep** (reclaim everything not
  marked).
- **Tri-color** = the bookkeeping algorithm that lets marking happen **while the program runs**.
  Objects are conceptually colored:
  - **white** = not yet proven reachable (candidate for collection),
  - **grey** = reachable but its children not yet scanned,
  - **black** = reachable and fully scanned.
  The collector scans greys→black until none remain; whatever's still **white at the end is
  garbage** and gets swept.
- **Concurrent** = mark/sweep run **alongside** your goroutines on the same cores, not in a long
  freeze.

### The two tiny stop-the-world pauses
The GC briefly pauses **all** goroutines only **twice**, very shortly, to set up and finish a
cycle safely (e.g., enable the **write barrier** that tracks pointer changes during concurrent
marking). These pauses are typically **well under a millisecond** — the headline GC feature. *The
heavy lifting (marking the whole heap) happens concurrently; only the bracketing is "the world
stops."*

---

## 4. When does it run, and can you tune it?

- The GC triggers based on **heap growth** since the last cycle, controlled by **`GOGC`**
  (default **100** = "run when the heap has grown ~100%, i.e., doubled"). Higher `GOGC` = fewer,
  larger collections (more memory, less CPU); lower = more frequent (less memory, more CPU).
- **`GOMEMLIMIT`** (1.19+) sets a **soft memory ceiling** — great in containers to avoid OOM
  kills by making the GC work harder as you approach the limit.
- You rarely call it manually (`runtime.GC()`), and shouldn't in normal code — let it self-tune.

---

## 5. Writing GC-friendly Go (practical, senior-level)

You don't fight the GC; you **make less garbage**:
- **Pre-size slices/maps** with `make([]T, 0, n)` / `make(map[K]V, n)` to avoid repeated regrow
  allocations (Lesson 03).
- **Reuse buffers** with **`sync.Pool`** for hot paths (e.g., per-request byte buffers).
- **Avoid unnecessary pointers/escapes** — keep values on the stack when you can; check with
  `-gcflags=-m`.
- **Use `strings.Builder`** instead of `+=` in loops.
- **Profile before optimizing:** `pprof` shows where allocations and CPU actually go — *measure,
  don't guess* (Lesson 11).

---

## 6. The whole memory story in one flow

```
you create a value
   │  compiler runs ESCAPE ANALYSIS
   ├─ doesn't escape → STACK → freed instantly on return (no GC)
   └─ escapes        → HEAP  → later reclaimed by the concurrent GC
                                 (mark reachable, sweep the rest;
                                  two sub-ms stop-the-world pauses per cycle)
```

---

## 🎤 Say this in the interview

- *"Go is **garbage-collected** for safety — no manual free, no use-after-free — using a
  **concurrent, tri-color mark-and-sweep** collector. Marking and sweeping run **alongside** my
  goroutines, so the stop-the-world pauses are just two tiny brackets, usually **sub-millisecond**."*
- *"The GC only manages the **heap**; stack values (that don't escape) are freed instantly. So I
  cut GC pressure by **minimizing escapes** (check with `-gcflags=-m`), **pre-sizing**
  slices/maps, and reusing buffers via **`sync.Pool`**."*
- *"It self-tunes via **`GOGC`** (default 100), and in containers I set **`GOMEMLIMIT`** to avoid
  OOM kills. I **profile with `pprof`** before optimizing."*

➡️ **Next:** [09 — Errors, defer, panic & recover](./09_Errors_Defer_Panic.md)
