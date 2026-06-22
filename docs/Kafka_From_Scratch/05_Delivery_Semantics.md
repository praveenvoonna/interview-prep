# 05 — Delivery Semantics & Exactly-Once

## 🧠 The One Idea

**Delivery semantics answer one question: "if something crashes, can a message be lost or
duplicated?"** Three answers: **at-most-once** (might lose, never duplicate), **at-least-once**
(never lose, might duplicate), **exactly-once** (never lose, never duplicate). Most systems run
at-least-once and make the *processing* idempotent; true exactly-once needs Kafka's
**transactions**.

The common one-liner: **"At-least-once by default; exactly-once via the idempotent producer +
transactions."**

---

## 1. The three guarantees

| Semantic | Lost? | Duplicate? | How you get it |
|---|---|---|---|
| **At-most-once** | Possible | No | Commit offset **before** processing |
| **At-least-once** | No | Possible | Commit offset **after** processing (the common default) |
| **Exactly-once** | No | No | Idempotent producer + transactions, or idempotent consumer |

---

## 2. At-least-once (the practical default)

- Process the record, **then** commit the offset.
- If you crash *after* processing but *before* committing, you'll reprocess → **duplicate**.
- This is fine **if your processing is idempotent** — e.g. `UPSERT` by key, or dedupe on an
  event id. Most real systems do exactly this. It's the pragmatic, recommended baseline.

---

## 3. Exactly-once semantics (EOS)

Two independent problems must be solved:

1. **Producer duplicates from retries** → solved by the **idempotent producer** (PID + sequence
   numbers, Lesson 03). Exactly-once *writes per partition*.
2. **The read-process-write cycle** (consume from topic A, produce to topic B, commit offset)
   must be **atomic** — all three succeed or none do → solved by **transactions**.

### Transactions

- The producer sets a **`transactional.id`** and wraps work in
  `beginTransaction()` … `sendOffsetsToTransaction()` … `commitTransaction()`.
- The **offset commit and the output records commit together**, atomically.
- Consumers reading the output set **`isolation.level=read_committed`** so they only see records
  from **committed** transactions (aborted ones are skipped).

```java
producer.initTransactions();
producer.beginTransaction();
producer.send(outRecord);
producer.sendOffsetsToTransaction(offsets, consumerGroupMetadata);
producer.commitTransaction();   // output + offset commit atomically
```

---

## 4. Important scope limits (say these to sound senior)

- Kafka EOS is **exactly-once within Kafka** (Kafka→processing→Kafka). It does **not** magically
  extend to an external database or a third-party API call.
- For a sink outside Kafka, you still need **idempotent writes** or the **transactional outbox**
  pattern on that system.
- EOS adds overhead (transaction coordination), so use it only where duplicates are truly
  unacceptable; otherwise **at-least-once + idempotent processing** is simpler and faster.

---

## 🎤 Say this in the interview

- *"Three semantics: at-most-once (commit before), at-least-once (commit after — the default),
  exactly-once. In practice I run at-least-once with **idempotent processing**."*
- *"True exactly-once needs two things: the **idempotent producer** for retry dedupe, and
  **transactions** to atomically commit output records + consumer offsets, with consumers on
  `read_committed`."*
- *"EOS is exactly-once **within Kafka** — crossing to an external DB still needs idempotency or
  an outbox."*

➡️ **Next:** [06 — Storage, retention & log compaction](./06_Storage_Retention_Compaction.md)
