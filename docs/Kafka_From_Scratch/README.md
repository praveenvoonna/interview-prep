# 🦄 Kafka From Scratch — A Crash Course (in plain English)

A from-zero deep dive into **Apache Kafka**: *what it is, why it exists, and how its internals
actually work* — explained in **naive, everyday language** with analogies, then the real
mechanics, then **interview-ready lines**.

> **How to read this:** go in order, 00 → 11. Each lesson starts with a plain-English analogy
> (**"The One Idea"**), then the real details, then **"Say this in the interview"** one-liners
> you can repeat almost verbatim. Don't skip the analogies — they're the hooks that make the
> rest stick.

---

## 📂 The lessons (read in order)

### Part A — The mental model (don't skip)
- **[00 — What is Kafka & why it exists](./00_What_Is_Kafka.md)** — the log, pub/sub vs
  queues, the problem Kafka solves.
- **[01 — Architecture big picture](./01_Architecture_Big_Picture.md)** — brokers, the
  cluster, ZooKeeper vs KRaft, the controller.

### Part B — The core objects (the 90% you use daily)
- **[02 — Topics, partitions & offsets](./02_Topics_Partitions_Offsets.md)** — the unit of
  parallelism and ordering.
- **[03 — Producers](./03_Producers.md)** — keys, the partitioner, `acks`, idempotence,
  batching & compression.
- **[04 — Consumers & consumer groups](./04_Consumers_And_Groups.md)** — group rebalancing,
  offset commits, lag.

### Part C — How it really works
- **[05 — Delivery semantics & exactly-once](./05_Delivery_Semantics.md)** — at-most/at-least/
  exactly-once, transactions.
- **[06 — Storage, retention & log compaction](./06_Storage_Retention_Compaction.md)** —
  segments, retention, compaction.
- **[07 — Replication, ISR & high availability](./07_Replication_And_HA.md)** — leaders,
  followers, ISR, `min.insync.replicas`.

### Part D — The ecosystem + running it
- **[08 — The Kafka ecosystem](./08_Ecosystem.md)** — Connect, Streams, Schema Registry,
  ksqlDB.
- **[09 — Operations, performance & gotchas](./09_Operations_And_Gotchas.md)** — tuning,
  monitoring, the classic mistakes.

### Part E — Practice
- **[10 — Hands-on lab](./10_Hands_On_Lab.md)** — run a broker, produce/consume, see
  partitions and groups for real.
- **[11 — Interview Q&A flashcards](./11_Interview_QA_Flashcards.md)** — rapid-fire questions
  with crisp answers.

---

## 🧠 The whole course in three sentences

1. **Kafka is a distributed, append-only commit log:** producers append records to **partitions**,
   consumers read them at their own pace by **offset** — storage and delivery are decoupled.
2. **Partitions are the unit of parallelism and ordering:** order is guaranteed *within* a
   partition, and a **consumer group** spreads partitions across its members.
3. **Durability comes from replication:** each partition has a **leader** and **followers**;
   `acks=all` + `min.insync.replicas` is the durability contract.

If you can say those three sentences and explain each, you've got the backbone.

---

## 🛠️ Tools for the lab (Lesson 10)
- **Docker / Podman** — easiest way to run a single-broker Kafka (KRaft mode, no ZooKeeper).
- **The console scripts** — `kafka-topics`, `kafka-console-producer`, `kafka-console-consumer`,
  `kafka-consumer-groups` (bundled in the Kafka download).

You've got this. Start at **[00](./00_What_Is_Kafka.md)**.
