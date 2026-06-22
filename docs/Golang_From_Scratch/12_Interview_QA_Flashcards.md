# 12 — Interview Q&A Flashcards

Read these **out loud**. Cover the answer, ask the question, then check. Two passes the night
before beat re-reading the lessons. Grouped by topic.

---

## A. Why Go / philosophy
- **Q: Why does Go exist / why pick it?**
  A: Built at Google for **large teams on large systems**: fast builds, one readable style
  (`gofmt`), concurrency in the language, and a single static binary. Simplicity over cleverness.
- **Q: Why is Go great for cloud/infra?**
  A: Cheap **goroutines** for massive concurrency + a **static binary** that drops into a
  `scratch` container + a **fast, low-pause GC**. That's why K8s/Docker/OpenShift/OCM are Go.
- **Q: Go's trade-offs?**
  A: Verbose `if err != nil`, late/limited generics, no exceptions, GC (not hard-real-time),
  very opinionated (unused vars are compile errors).

## B. How it runs
- **Q: Compiled or interpreted?**
  A: Compiled **ahead-of-time to native machine code**, **statically linked** → one
  self-contained binary, no VM/interpreter to install.
- **Q: Is there a runtime?**
  A: Yes — a small runtime (**scheduler + GC + allocator**) is **linked into the binary**, but
  it's not a separate VM, and startup is instant.
- **Q: Package vs module?**
  A: A **package** organizes code (capitalized = exported); a **module** (`go.mod`) is the unit
  of versioned dependency management. One `go` tool builds/tests/formats/manages deps.

## C. Values, types, memory
- **Q: Pass-by-value or reference?**
  A: **Always pass-by-value** — copies unless you use a **pointer** (which copies an address).
  No pointer arithmetic.
- **Q: Stack vs heap — who decides?**
  A: The **compiler**, via **escape analysis**: if a value's address escapes the function it
  goes to the **heap** (GC-managed); otherwise the **stack** (freed instantly). Check with
  `-gcflags=-m`.
- **Q: Zero values?**
  A: Every variable is auto-initialized: `0`/`""`/`false`/`nil`. No uninitialized garbage.

## D. Slices, maps, strings
- **Q: What is a slice?**
  A: A **3-word header — pointer, len, cap — over a backing array**. Copies share the array.
- **Q: append gotcha?**
  A: It may **reallocate** when cap runs out, returning a new array — always reassign; pre-size
  with `make([]T, 0, n)` to cut allocations/aliasing surprises.
- **Q: map gotchas?**
  A: **Writing a nil map panics**; missing key returns zero (use comma-ok); order is random;
  **concurrent writes crash** → guard with Mutex or use `sync.Map`.
- **Q: string vs rune?**
  A: A string is an **immutable UTF-8 byte slice**; `len` counts **bytes**; a **rune** is a
  Unicode code point. `range` decodes runes; use `strings.Builder` to build efficiently.

## E. Methods & interfaces
- **Q: How are interfaces satisfied?**
  A: **Implicitly** — any type with the methods satisfies it; no `implements`. Keep them small;
  "accept interfaces, return structs."
- **Q: How is an interface represented?**
  A: A **two-word pair: (type descriptor, value pointer)**; method calls dispatch via the type's
  itable.
- **Q: The nil-interface trap?**
  A: An interface is nil only if **both** words are nil. A **typed nil pointer** stored in an
  interface is **not nil** (type word set). Return literal `nil` for no-error.
- **Q: Value vs pointer receiver?**
  A: Pointer receiver to **mutate** or avoid big copies; use consistently across the type.

## F. Concurrency (the big one)
- **Q: What's a goroutine?**
  A: A runtime-managed lightweight thread (~2KB growable stack, user-space switches) — run
  **millions** vs thousands of OS threads.
- **Q: Explain the GMP scheduler.**
  A: **M:N** — **G**oroutines run on **M** OS threads, gated by **P** contexts (=`GOMAXPROCS`).
  At most `GOMAXPROCS` run in parallel; rest queue. Features: **work-stealing**, **P handoff** on
  blocking syscalls, **network poller** for I/O, **async preemption** of CPU hogs.
- **Q: Concurrency vs parallelism?**
  A: Concurrency = **structuring** independent work; parallelism = **running** it simultaneously
  on cores. Goroutines give concurrency; the scheduler maps it to parallelism.
- **Q: Channel — unbuffered vs buffered?**
  A: Unbuffered = synchronous **hand-off** (rendezvous); buffered = decouples up to N (and works
  as a semaphore).
- **Q: Channel rules?**
  A: Only the **sender closes**; **send-on-closed panics**, **receive-on-closed** = zero+`!ok`;
  `range` drains till close; **nil channel blocks forever** (disables a select case).
- **Q: What does select do?**
  A: Waits on multiple channel ops; **random** among ready ones; `default` = non-blocking; with
  `time.After`/`ctx.Done()` gives timeouts/cancellation.
- **Q: Channels vs mutex?**
  A: Channels to **communicate/pass ownership**; Mutex to **protect shared state**. "Channels
  orchestrate; mutexes serialize."
- **Q: What's a data race & how do you find it?**
  A: Two goroutines touch the same memory, ≥1 writing, unsynchronized. Find with **`go run/test
  -race`** — it prints the exact goroutines/lines. I always run concurrent code with `-race`.
- **Q: What is context for?**
  A: **Cancellation/deadlines/timeouts**. `ctx.Done()` is a channel that **closes** to broadcast
  stop; pass `ctx` first, `defer cancel()`, it propagates to children. Core to controllers.
- **Q: WaitGroup usage?**
  A: `Add` before `go`, `Done` via `defer`, `Wait` to join. (`main` exiting kills goroutines.)

## G. Runtime, errors, generics
- **Q: How does Go's GC work?**
  A: **Concurrent, tri-color mark-and-sweep** — marks reachable objects and sweeps the rest
  while the program runs; only **two tiny stop-the-world** brackets, usually **sub-ms**. Manages
  the **heap** only.
- **Q: How do you reduce GC pressure?**
  A: Minimize **escapes** (`-gcflags=-m`), **pre-size** slices/maps, reuse buffers via
  **`sync.Pool`**, `strings.Builder`; set **`GOMEMLIMIT`** in containers; **profile with pprof**.
- **Q: How does Go handle errors?**
  A: **Errors as values** (`if err != nil`), not exceptions. **Wrap** with `%w`, inspect with
  **`errors.Is`/`errors.As`**.
- **Q: defer — order & arg evaluation?**
  A: Runs on function exit (normal or panic), **LIFO**; **args evaluated at `defer` time**, call
  at return time. Great for `Close`/`Unlock`/`cancel`.
- **Q: panic/recover — when?**
  A: Errors for expected failures; **panic only for truly unrecoverable** bugs. `recover` (in a
  `defer`) at a **top-level handler** so one bad request doesn't crash the server.
- **Q: Generics vs interfaces?**
  A: Interface = "any type with these **methods**" (dynamic dispatch); generic = "this code
  specialized for a **type**" (compile-time, type-safe). Use generics for containers/algorithms,
  sparingly.

---

## 🎯 The 3-sentence backbone (if you blank, say this)
1. *"Go is a small, fast, statically-typed language that compiles to a **single static binary** —
   simplicity and readability at team scale."*
2. *"Concurrency is built in: **cheap goroutines + channels** on an **M:N GMP scheduler**, so I
   write straightforward concurrent code and verify it with `-race`."*
3. *"A **concurrent, low-pause GC** plus **escape analysis** give me C-like speed with memory
   safety — which is exactly why Kubernetes, OpenShift, and OCM are written in Go."*

Good luck — go get it. 🚀
