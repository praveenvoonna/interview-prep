# 🏗️ System Design From Scratch — A Crash Course (in plain English)

A from-zero course for **design rounds**: how to take a vague prompt, scope it, and reason about
scaling, data, consistency and failure out loud. Tuned to your background — distributed
control-plane systems, etcd/consensus, queues — so your real experience becomes the examples.

> **How to read this:** go in order, 00 → 09. Each lesson starts with a plain-English analogy
> (**"The One Idea"**), then the real building block, then **"Say this in the interview"** lines.

> **Status:** 📝 *Syllabus ready — lessons to be written.* This README is the outline and the
> reference you can study from today.

---

## 📂 The lessons (planned, read in order)

### Part A — The method
- **00 — How to approach a design** — requirements → estimates → API → data → scale → failure.

### Part B — The building blocks
- **01 — Scaling & load balancing** — vertical vs horizontal, stateless services, L4/L7 LB.
- **02 — Caching** — where to cache, invalidation, write-through vs write-back, hot keys.
- **03 — Databases & sharding** — SQL vs NoSQL choice, partitioning, hot-shard problems.
- **04 — Queues & async** — decoupling, backpressure, retries, idempotency (ties to Kafka).

### Part C — The hard guarantees
- **05 — Consistency & CAP** — strong vs eventual, quorums, the real trade-offs.
- **06 — Consensus** — Raft/leader election, why etcd exists, where you've used it.
- **07 — Observability & resilience** — metrics/logs/traces, timeouts, retries, circuit breakers,
  graceful degradation (ties to your MTTR/structured-logging story).

### Part D — Practice
- **08 — Worked examples** — e.g. a rate limiter, a metrics pipeline, a config/control plane.
- **09 — Interview Q&A flashcards** — trade-off prompts and crisp framing lines.

---

## 🧠 The whole course in three sentences
1. **Design is a conversation about trade-offs:** there's no single right answer — clarify
   requirements, state assumptions, and justify each choice.
2. **Scale by removing state and adding partitions:** stateless services + sharded data +
   async queues is the backbone of most designs.
3. **Guarantees cost something:** strong consistency, exactly-once and low latency all trade
   against each other — name the trade-off you're making.

---

## 🔗 Companion material
- **[Kafka From Scratch](../Kafka_From_Scratch/)** — for the queue/async lessons.
- **[Databases From Scratch](../Databases_From_Scratch/)** — for the data-layer lessons.

When the lessons are written, start at **00**.
