# 06 — Channels & Select (the internals)

## 🧠 The One Idea

**A channel is a thread-safe pipe: one goroutine puts a value in, another takes it out, and Go
handles all the locking so you never touch a mutex.** Go's motto: **"Don't communicate by
sharing memory; share memory by communicating."** Instead of many goroutines fighting over one
variable (and needing locks), you **pass the data through a channel** so only one goroutine owns
it at a time. *Analogy: a channel is a **conveyor belt** between workers — the sender places a
box, the receiver picks it up; they never reach into each other's workspace.*

---

## 1. Channel basics

```go
ch := make(chan int)     // a channel that carries ints
ch <- 42                 // SEND: put 42 in   (arrow points INTO the channel)
x := <-ch                // RECEIVE: take a value out
close(ch)                // no more values will be sent
```

- Channels are **typed** (`chan int` only carries ints) and **safe for concurrent use** — that's
  the whole point; no extra locking needed.
- Think of `<-` as an **arrow showing direction**: `ch <- v` sends in, `v := <-ch` takes out.

---

## 2. Unbuffered vs buffered (the crucial distinction)

### Unbuffered — `make(chan int)`
- Capacity 0. A send **blocks until** a receiver is ready, and a receive **blocks until** a
  sender is ready — they meet at the same instant. This is a **synchronization point / hand-off
  (rendezvous)**.
- Use it to **synchronize** two goroutines: "you can't proceed until I've handed this over."
- *Analogy: handing someone a baton in a relay — you both must be there at the moment of
  exchange.*

### Buffered — `make(chan int, 3)`
- Has a queue of capacity N. A **send blocks only when the buffer is full**; a **receive blocks
  only when it's empty**. Sender and receiver are **decoupled** in speed up to N items.
- Use it to **smooth bursts** (a producer faster than a consumer) or as a **semaphore** to cap
  concurrency (a buffer of size N = "at most N at once").
- *Analogy: a mailbox with N slots — you can drop letters without waiting, until it's full.*

---

## 3. Closing, ranging, and the rules (memorize these)

- **Only the sender closes a channel** (the side that knows no more will come). Closing signals
  "done" to receivers.
- **Receiving from a closed channel** returns immediately: the **zero value** and `ok == false`:
  ```go
  v, ok := <-ch        // ok == false means the channel is closed and drained
  for v := range ch {  // loops until the channel is closed — the idiomatic consumer
      use(v)
  }
  ```
- **The panic rules (classic gotchas):**
  - **Send on a closed channel → panic.**
  - **Close a closed channel (or close twice) → panic.**
  - **Receive from a closed channel → fine** (zero value, immediately).
- **`nil` channel:** send/receive on it **block forever** — useful to **disable a `select`
  branch** dynamically (set the channel to `nil` to "turn it off").

---

## 4. What a channel is under the hood (the internal)

A channel is a pointer to a runtime struct (**`hchan`**) that contains:
- a **ring buffer** (for buffered channels) + counts,
- a **lock** (yes, internally it uses a mutex — Go hides it so *you* don't),
- two **wait queues**: goroutines blocked **sending** and goroutines blocked **receiving**.

**How a blocked goroutine works (the elegant part):** when a goroutine blocks on a channel, the
runtime **parks** it (removes it from its P, costs no CPU) and puts it on the channel's wait
queue. When the matching operation happens, the runtime **wakes** it and reschedules it. So
"blocking on a channel" is **cheap** — it's not a spinning thread, just a parked goroutine the
scheduler will resume. *This is how Go runs a million goroutines that are "waiting."*

---

## 5. `select` — waiting on multiple channels

`select` lets a goroutine **wait on several channel operations at once** and proceed with
whichever is ready first.

```go
select {
case v := <-in:            // got input
    process(v)
case out <- result:        // could send a result
    sent()
case <-time.After(time.Second):   // timeout
    fmt.Println("timed out")
case <-ctx.Done():         // cancellation (Lesson 07)
    return
default:                   // optional: makes select NON-blocking
    fmt.Println("nothing ready right now")
}
```

- If **several cases are ready, `select` picks one at random** (fairness — prevents starvation).
- **`default`** makes it **non-blocking** (do something else if nothing's ready).
- Combine with **`time.After`** for **timeouts** and **`ctx.Done()`** for **cancellation** —
  this is the backbone of robust Go servers and controllers.

*Internals:* `select` registers the goroutine on **all** the cases' wait queues at once; the
first channel that becomes ready wakes it, and it deregisters from the rest.

---

## 6. Channels vs mutexes — which to use

Both are valid; pick by intent:
- **Channels** — when you're **passing ownership of data** or **coordinating/signaling** between
  goroutines (pipelines, work distribution, "done" signals). *Communicate.*
- **Mutex (`sync.Mutex`)** — when you're **protecting shared state** that goroutines genuinely
  share (a counter, a cache, a map). *Guard.* (Lesson 07.)
- Idiom: *"Channels orchestrate; mutexes serialize."* Don't force a channel where a one-line
  mutex is clearer.

---

## 🎤 Say this in the interview

- *"A channel is a typed, concurrency-safe pipe — **'share memory by communicating.'**
  **Unbuffered** is a synchronous hand-off (rendezvous); **buffered** decouples producer and
  consumer up to N and doubles as a semaphore."*
- *"Rules: only the **sender closes**; **send-on-closed panics**, **receive-on-closed** gives
  zero+`!ok`; `range` drains until close; a **nil channel blocks forever** to disable a select
  case."*
- *"Under the hood it's an `hchan` with a lock and send/receive **wait queues**; blocked
  goroutines are **parked** (zero CPU) and woken on the matching op. `select` waits on many
  channels, picks a ready one at random, and with `time.After`/`ctx.Done()` gives timeouts and
  cancellation."*

➡️ **Next:** [07 — Concurrency patterns, sync & context](./07_Concurrency_Patterns_Sync_Context.md)
