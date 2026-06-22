# 02 — Concurrency

## 🧠 The One Idea

**When multiple threads touch the same data, you need rules so they don't corrupt it — Java gives you
locks, atomics, and a memory model for exactly that.** The hard part isn't starting threads; it's
making shared state **safe** without deadlocking or killing performance.

The one-liner: **"Use an ExecutorService not raw threads; protect shared mutable state with
synchronization or concurrent collections; `volatile` is for visibility, not atomicity."**

---

## 1. Threads & the executor

- A **thread** is an independent path of execution; they share the heap.
- Don't hand-manage threads — use an **`ExecutorService`** (a thread pool):

```java
ExecutorService pool = Executors.newFixedThreadPool(4);
Future<Integer> f = pool.submit(() -> compute());
int result = f.get();          // blocks for the result
pool.shutdown();
```

Pools cap concurrency, reuse threads, and give you `Future`/`CompletableFuture` for results.

---

## 2. The core problem: shared mutable state

- Two threads incrementing the same counter can **interleave** and lose updates (a **race
  condition**).
- The fix: make the critical section **mutually exclusive** (only one thread at a time).

---

## 3. synchronized & locks

- **`synchronized`** (method or block) acquires an object's intrinsic lock → one thread in the
  critical section at a time.
- **`ReentrantLock`** is the explicit, more flexible version (tryLock, timeouts, fairness).
- **Deadlock:** two threads each hold a lock the other needs → both stuck. Avoid by **consistent
  lock ordering**.

---

## 4. volatile vs atomic vs synchronized

- **`volatile`:** guarantees **visibility** (other threads see the latest value) but **not
  atomicity** — `count++` is still unsafe.
- **`Atomic*`** (`AtomicInteger`): lock-free atomic operations (CAS) — great for counters.
- **`synchronized`/locks:** both visibility **and** atomicity for multi-step critical sections.

**Use volatile for flags, atomics for counters, locks for compound operations.**

---

## 5. The memory model & safe tools

- The **Java Memory Model** defines when one thread's writes become visible to another
  (happens-before); `synchronized`/`volatile`/atomics establish it.
- Prefer **`java.util.concurrent`**: `ConcurrentHashMap`, `BlockingQueue`, `CompletableFuture`,
  `CountDownLatch` — correct and tuned, so you rarely write low-level locking.
- Modern Java: **virtual threads** (Project Loom) make blocking cheap at huge scale.

---

## 🎤 Say this in the interview

- *"I use an ExecutorService rather than raw threads — pooled, with Futures/CompletableFuture for
  results — and shut it down cleanly."*
- *"`volatile` gives visibility but not atomicity, so `count++` needs an AtomicInteger or a lock;
  synchronized/locks cover multi-step critical sections."*
- *"I avoid deadlocks with consistent lock ordering and prefer java.util.concurrent collections like
  ConcurrentHashMap over hand-rolled synchronization."*

➡️ **Next:** [03 — IoC & dependency injection](./03_IoC_And_Dependency_Injection.md)
