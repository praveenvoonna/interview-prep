# 🗄️ Databases From Scratch — A Crash Course (in plain English)

A from-zero course on the database knowledge interviewers actually probe: **indexes**,
**transactions/ACID**, query plans, and how relational vs NoSQL differ — across SQL and NoSQL
systems like MySQL, PostgreSQL, MongoDB, Oracle, and DynamoDB.

> **How to read this:** go in order, 00 → 07. Each lesson starts with a plain-English analogy
> (**"The One Idea"**), then the real mechanics, then **"Say this in the interview"** lines.

> **Status:** 📝 *Syllabus ready — lessons to be written.* This README is the outline and the
> reference you can study from today.

---

## 📂 The lessons (planned, read in order)

### Part A — The landscape
- **00 — Relational vs NoSQL** — tables vs documents/KV/wide-column, when each fits.

### Part B — The internals interviewers love
- **01 — Indexes** — B-tree vs hash vs GIN, composite indexes, covering indexes, when an index
  *hurts*; how to read what an index is doing.
- **02 — Transactions & ACID** — atomicity/consistency/isolation/durability, isolation levels,
  the anomalies (dirty/non-repeatable/phantom), MVCC.
- **03 — Query plans & optimization** — `EXPLAIN`, scans vs seeks, joins, why a query is slow.

### Part C — Common NoSQL systems
- **04 — MongoDB** — documents, indexes, the aggregation pipeline, when to denormalise.
- **05 — DynamoDB data modeling** — partition/sort keys, single-table design, GSIs, hot
  partitions.

### Part D — Scale & recall
- **06 — Replication & sharding** — leader/follower, read replicas, partitioning strategies,
  consistency trade-offs.
- **07 — Interview Q&A flashcards** — index/isolation/sharding rapid-fire.

---

## 🧠 The whole course in three sentences
1. **Indexes are the #1 database interview topic:** a B-tree index turns a full scan into a
   logarithmic seek — but every index costs writes and storage.
2. **ACID is about what happens under concurrency and crashes:** isolation levels are the dial
   between correctness and throughput.
3. **NoSQL trades flexibility for scale:** you model around your access patterns (especially in
   DynamoDB), not around normalised entities.

---

## 🔗 Companion material
- **[AWS From Scratch](../AWS_From_Scratch/)** — DynamoDB/RDS context.
- **[System Design From Scratch](../System_Design_From_Scratch/)** — sharding & consistency at system level.

When the lessons are written, start at **00**.
