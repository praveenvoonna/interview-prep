# 05 — Goroutines & the GMP Scheduler

## 🧠 The One Idea

**A goroutine is a "function that runs on its own, super cheap to start" — and Go has its own
mini operating system inside the binary that juggles millions of them onto a handful of real
CPU threads.** You write `go doWork()` and Go handles the rest. *Analogy: OS threads are
**full-time employees** — expensive to hire, you can only afford a few thousand. Goroutines are
**tasks on a shared to-do list** — you can have millions, and a small team of workers (threads)
picks them off the list.* That cheapness is why Go is the language of servers and controllers.

---

## 1. What a goroutine actually is

- A **goroutine** is a function executing **concurrently** with others, managed by the **Go
  runtime** (not the OS directly). Start one with the `go` keyword:
  ```go
  go doWork()       // launches doWork concurrently; the caller keeps going
  ```
- **Why they're cheap:**
  - **Tiny stacks** — a goroutine starts with a **~2KB** stack that **grows and shrinks** on
    demand. An OS thread reserves **1–8MB**. So a goroutine costs ~1000× less memory.
  - **User-space scheduling** — switching between goroutines doesn't need an expensive trip into
    the OS kernel; the Go runtime swaps them in user space, much faster than an OS context switch.
- **The numbers:** you can run **millions** of goroutines; you can realistically run only
  **thousands** of OS threads. *This is the whole reason Go scales to huge concurrency.*

---

## 2. Concurrency ≠ parallelism (say this clearly)

- **Concurrency** = *structuring* a program as independent tasks that can make progress
  **interleaved** (dealing with many things at once).
- **Parallelism** = *actually running* tasks **at the same instant** on multiple CPU cores.
- **Goroutines give you concurrency**; the scheduler maps that concurrency onto real cores to
  achieve parallelism (up to `GOMAXPROCS` cores). *A one-core machine can run a million
  goroutines concurrently — just not in parallel.* (Rob Pike: "Concurrency is about dealing
  with lots of things at once; parallelism is about doing lots of things at once.")

---

## 3. The GMP model — how scheduling works (the core internal)

This is *the* Go internals interview question. Go uses an **M:N scheduler**: it multiplexes **M**
goroutines onto **N** OS threads. Three pieces:

- **G = Goroutine** — your task + its tiny stack and state.
- **M = Machine** — an **OS thread**, the thing the kernel actually runs on a CPU.
- **P = Processor** — a **scheduler context / permit to run Go code**. The number of Ps =
  **`GOMAXPROCS`** (defaults to the number of CPU cores). Each P has its own **local run queue**
  of ready goroutines.

**The rule: an M must hold a P to execute Go code.** So at any moment, **at most `GOMAXPROCS`
goroutines run in parallel** (one per P), while thousands more wait in run queues.

```
   Gs (millions) ── sit in ──▶ P's local run queues (GOMAXPROCS of them)
                                     │ each P is attached to
                                     ▼
                                 an M (OS thread) ──runs on──▶ a CPU core
```

*Analogy: **P = a checkout lane** (limited number), **M = a cashier**, **G = customers**. A
cashier needs an open lane to serve customers; customers line up in each lane's queue.*

---

## 4. The clever parts (what makes it fast & fair)

- **Work-stealing:** if a P's local queue empties, that P **steals half** the goroutines from a
  busy P's queue (and checks a global queue). Result: **no core sits idle** while another is
  swamped — automatic load balancing.
- **Syscall handoff (handling blocking):** if a goroutine makes a **blocking syscall** (e.g.,
  disk read), its M blocks in the kernel — but the runtime **detaches the P** and hands it to
  another (or new) M, so the **other goroutines keep running**. One slow syscall doesn't freeze
  your program. When the syscall returns, the goroutine grabs a P again.
- **Network poller:** blocking on network I/O doesn't waste a thread at all — the goroutine is
  **parked** and woken by an OS event mechanism (epoll/kqueue) when data is ready. This is why a
  Go server can hold **hundreds of thousands of idle connections** cheaply.
- **Async preemption (Go 1.14+):** a goroutine stuck in a tight CPU loop is **interrupted via a
  signal** so it can't hog a P forever — keeps scheduling fair without cooperative yield points.

---

## 5. You must synchronize — goroutines need coordination

Launching a goroutine is fire-and-forget; the program **won't wait** for it. Two pitfalls:

- **`main` returning kills everything:** when `main()` ends, all goroutines die — even mid-work.
- **Data races:** two goroutines touching the same variable (one writing) **without
  synchronization** = undefined behavior/crashes.

The fix is the next two lessons: **channels** (communicate) and **`sync`/`context`**
(coordinate). The minimal "wait for them to finish" tool:

```go
var wg sync.WaitGroup
for i := 0; i < 3; i++ {
    wg.Add(1)
    go func(n int) {
        defer wg.Done()      // signal "I'm done"
        fmt.Println("worker", n)
    }(i)                     // pass i in — don't close over the loop variable
}
wg.Wait()                    // block until all 3 call Done()
```

> **Always synchronize.** *Never* assume a goroutine finished just because time passed. (Loop-
> variable capture was a classic bug; Go 1.22 made each iteration get its own variable, but
> passing it as an argument is still the clearest habit.)

---

## 🎤 Say this in the interview

- *"A goroutine is a runtime-managed lightweight thread — ~2KB growable stack, user-space
  switches — so I can run **millions**, versus thousands of OS threads."*
- *"Go uses an **M:N GMP scheduler**: **G**oroutines run on **M** OS threads, gated by **P**
  contexts (= `GOMAXPROCS`). At most `GOMAXPROCS` run in parallel; the rest queue. It does
  **work-stealing**, hands off the **P on blocking syscalls**, parks network I/O on a poller, and
  **async-preempts** CPU hogs."*
- *"**Concurrency is structure; parallelism is simultaneous execution.** Goroutines give
  concurrency; the scheduler maps it to cores. And I **always synchronize** with WaitGroup/
  channels — `main` exiting kills goroutines."*

➡️ **Next:** [06 — Channels & select (internals)](./06_Channels_And_Select.md)
