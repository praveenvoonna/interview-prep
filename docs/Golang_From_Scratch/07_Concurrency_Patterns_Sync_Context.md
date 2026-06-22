# 07 — Concurrency Patterns, sync & context

## 🧠 The One Idea

**Channels are the building blocks; the `sync` package and `context` are the seatbelts and the
emergency brake.** You use a few standard *patterns* (wait for workers, protect shared state,
cancel everything cleanly) over and over. Master **WaitGroup + Mutex + context + the worker
pool**, and you can write correct concurrent Go. *Analogy: goroutines are workers, channels are
the conveyor belts, `sync` is the "everyone clock out before we close" rule, and `context` is
the building-wide PA system that says "everybody stop, now."*

---

## 1. `sync.WaitGroup` — "wait for all goroutines to finish"

The fix for "`main` exits before workers finish" (Lesson 05).

```go
var wg sync.WaitGroup
for _, job := range jobs {
    wg.Add(1)                 // +1 BEFORE starting the goroutine
    go func(j Job) {
        defer wg.Done()       // -1 when it finishes (defer = even on early return)
        handle(j)
    }(job)
}
wg.Wait()                     // blocks until the counter hits 0
```

- **`Add` before the `go`**, **`Done` via `defer`**, **`Wait`** to join. *Counter analogy: Add =
  punch in, Done = punch out, Wait = "lock the doors when count is 0."*

---

## 2. `sync.Mutex` — protect shared state

When goroutines genuinely **share a variable** (a counter, a cache, a map), guard it so only one
touches it at a time.

```go
var (
    mu    sync.Mutex
    count int
)
func incr() {
    mu.Lock()
    defer mu.Unlock()         // always unlock — defer makes it exception-safe
    count++                   // critical section: only one goroutine here at a time
}
```

- **`sync.RWMutex`** — many concurrent **readers** OR one **writer**; use when reads vastly
  outnumber writes.
- **`sync.Once`** — run something **exactly once** (lazy init/singletons): `once.Do(setup)`.
- **`sync.Map`** — a concurrent map for specific patterns (many goroutines, disjoint keys); for
  most cases a plain map + Mutex is clearer.

---

## 3. The race detector — your safety net (huge for interviews)

A **data race** = two goroutines access the same memory, at least one writes, with no
synchronization. The result is undefined (corruption, crashes). They're invisible in normal
runs but Go can find them:

```bash
go run -race main.go      # or: go test -race ./...
```

The detector instruments memory accesses and **prints the exact two goroutines and lines** that
raced. *Say in the interview:* **"I always run my concurrent code and tests with `-race`."** It's
a signal of real concurrency experience.

---

## 4. `context` — cancellation, deadlines & timeouts (essential for servers)

`context.Context` is how you tell a tree of goroutines **"stop now"** — on user cancel, timeout,
or shutdown. It's the **first parameter** of basically every Go API that does I/O or blocks
(`func F(ctx context.Context, ...)`).

```go
ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
defer cancel()                 // ALWAYS call cancel to release resources

select {
case res := <-doWork(ctx):
    use(res)
case <-ctx.Done():             // fires on timeout OR cancel
    return ctx.Err()           // context.DeadlineExceeded or context.Canceled
}
```

- **How it works:** `ctx.Done()` is a **channel that closes** when the context is cancelled —
  so every waiting goroutine's `<-ctx.Done()` unblocks at once (a broadcast via close).
- **Propagation:** you derive child contexts (`WithCancel`, `WithTimeout`, `WithDeadline`); the
  cancel signal **flows down to all children** — cancel the parent, the whole tree stops. *This
  is the PA-system analogy.*
- **Rules:** pass `ctx` as the **first arg**, **never store it in a struct**, **always `defer
  cancel()`**, use `WithValue` sparingly (only request-scoped data, not optional params).
- This is *everywhere* in Kubernetes/controllers — a reconcile gets a `ctx` and must bail out
  promptly when it's cancelled.

---

## 5. The worker pool — the pattern to know cold

Bounded concurrency: N workers pull jobs from a channel and push results to another. *Don't spawn
a goroutine per job for huge workloads — cap them.*

```go
func pool(jobs <-chan int, results chan<- int, workers int) {
    var wg sync.WaitGroup
    for i := 0; i < workers; i++ {       // start N workers
        wg.Add(1)
        go func() {
            defer wg.Done()
            for j := range jobs {        // each pulls until jobs is closed
                results <- j * j
            }
        }()
    }
    wg.Wait()                            // wait for all workers
    close(results)                       // then signal "no more results"
}
```

- **Note the directional channel types:** `<-chan` (receive-only) and `chan<-` (send-only) make
  intent explicit and compiler-checked.
- Other patterns to name-drop: **fan-out/fan-in** (spread work to many goroutines, merge
  results), **pipelines** (stages connected by channels), **rate limiting** (a buffered channel
  or `time.Ticker`).

---

## 6. Putting the toolbox together
- **Wait for goroutines** → `sync.WaitGroup`.
- **Protect shared state** → `sync.Mutex` / `RWMutex` (or pass data via channels instead).
- **Run once** → `sync.Once`.
- **Stop a tree of work** → `context` (`Done()` channel + propagation).
- **Bound concurrency** → worker pool / buffered-channel semaphore.
- **Verify correctness** → `-race`.

---

## 🎤 Say this in the interview

- *"I join goroutines with **WaitGroup** (Add/Done/Wait), protect shared state with
  **Mutex/RWMutex** (Unlock via `defer`), and lazy-init with **Once** — or I avoid shared state
  by passing data through **channels**."*
- *"For cancellation I use **`context`** — `ctx.Done()` is a channel that **closes** to broadcast
  stop; I pass `ctx` as the first arg, `defer cancel()`, and it propagates to children. It's how
  controllers bail out of a reconcile cleanly."*
- *"For bounded concurrency I use a **worker pool** with directional channels, and I **always
  test with `-race`** to catch data races."*

➡️ **Next:** [08 — Memory & the garbage collector](./08_Memory_And_GC.md)
