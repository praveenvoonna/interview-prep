# 🐹 Go From Scratch — Why It's Better & How It Works (in plain English)

A from-zero deep dive into **Go (Golang)**: *why the language exists, why it's a great fit for
cloud/infra work, and how its internals actually work* — explained in **naive, everyday
language** with analogies, then the real mechanics, then interview-ready lines.

> Companion to the `Kubernetes_From_Scratch` folder. Kubernetes, Docker, OpenShift, and OCM
> are **all written in Go** — so understanding Go internals is exactly what makes you credible
> for cloud-native and infrastructure roles.

> **How to read each lesson:** start with **"The One Idea"** (a plain analogy), then the
> details, then **"Say this in the interview."** Don't skip the analogies — they're the hooks.

---

## 📂 The lessons (read in order)

### Part A — Why Go, and how it runs
- **[00 — Why Go exists & why it's better](./00_Why_Go_Exists.md)** — the problems Go was
  designed to solve; simplicity, speed, concurrency, static binaries.
- **[01 — How a Go program runs](./01_How_Go_Runs.md)** — compiler vs interpreter, **static
  binaries**, the runtime, modules & the toolchain.

### Part B — The building blocks (with internals)
- **[02 — Values, types, structs & pointers](./02_Values_Types_Pointers.md)** — value vs
  pointer semantics, zero values, **stack vs heap**, escape analysis intro.
- **[03 — Slices, maps & strings (internals)](./03_Slices_Maps_Strings.md)** — the headers
  behind them, growth, the famous gotchas.
- **[04 — Methods & interfaces (internals)](./04_Methods_And_Interfaces.md)** — the
  `(type, value)` pair, implicit satisfaction, the empty interface, `nil`-interface trap.

### Part C — Concurrency (Go's superpower) + internals
- **[05 — Goroutines & the GMP scheduler](./05_Goroutines_And_Scheduler.md)** — what a
  goroutine really is and how Go schedules millions of them.
- **[06 — Channels & select (internals)](./06_Channels_And_Select.md)** — typed pipes,
  buffered vs unbuffered, how `select` works under the hood.
- **[07 — Concurrency patterns, sync & context](./07_Concurrency_Patterns_Sync_Context.md)** —
  WaitGroup, Mutex, the race detector, worker pools, cancellation with `context`.

### Part D — The runtime that makes it pleasant
- **[08 — Memory & the garbage collector](./08_Memory_And_GC.md)** — escape analysis, the
  concurrent tri-color GC, why pauses are tiny.
- **[09 — Errors, defer, panic & recover](./09_Errors_Defer_Panic.md)** — errors as values,
  wrapping, `defer` internals, when to panic.
- **[10 — Generics, stdlib & tooling](./10_Generics_Stdlib_Tooling.md)** — type parameters,
  the batteries-included stdlib, and the tools that make Go productive.

### Part E — Practice
- **[11 — Hands-on lab](./11_Hands_On_Lab.md)** — run programs that *show* escape analysis,
  the race detector, goroutines, and benchmarks.
- **[12 — Interview Q&A flashcards](./12_Interview_QA_Flashcards.md)** — rapid-fire across
  every topic.

---

## 🗓️ Suggested plan

| Time | Do this |
|---|---|
| **Session 1 (2 hrs)** | Lessons **00–04** — why Go + the building blocks. |
| **Session 2 (2 hrs)** | Lessons **05–07** — concurrency (the most-asked topic). |
| **Session 3 (1.5 hrs)** | Lessons **08–10** — runtime, errors, generics/tooling. |
| **Session 4 (1 hr)** | Lesson **11 lab** — run it; then **12 flashcards** out loud. |

**If you only have 90 minutes:** read **00, 02, 05, 06** and the **12 flashcards** — they carry
the most interview weight (concurrency + memory model).

---

## 🧠 The whole language in three sentences

1. **Go optimizes for *simplicity at scale*** — a small, boring language that big teams can read
   and ship fast, compiling to a single static binary with no runtime to install.
2. **Concurrency is built into the language** — cheap **goroutines** + **channels**, scheduled
   by Go's own **M:N scheduler**, so you write straightforward concurrent code.
3. **A runtime with a garbage collector and escape analysis** handles memory for you, with
   tiny GC pauses — you get C-like speed with Python-like ease.

---

## 🛠️ Tools for the lab (Lesson 11)
- **`go`** (1.21+) — compiler + everything (`go run`, `go build`, `go test`, `go vet`).
- Built-ins you'll use: **`-race`** (race detector), **`-gcflags=-m`** (escape analysis),
  **`go test -bench`** (benchmarks), **`pprof`** (profiling).

Start at **[00 — Why Go exists](./00_Why_Go_Exists.md)**.
