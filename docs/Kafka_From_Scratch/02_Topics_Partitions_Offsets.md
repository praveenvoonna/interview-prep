# 02 — Topics, Partitions & Offsets

## 🧠 The One Idea

**A topic is a labelled filing cabinet; partitions are the drawers inside it.** You split a
topic into partitions so many people can file and read in parallel — but order is only
guaranteed *within a single drawer*, not across drawers. The **offset** is the slot number
inside one drawer. Pick the right "key" and related records always land in the same drawer, so
their order is preserved.

The common one-liner: **"Partitions are Kafka's unit of parallelism and ordering."**

---

## 1. Topics

- A **topic** is a named stream of records, e.g. `orders`, `clicks`, `payments`.
- It's a **logical** name; the real data lives in its **partitions**.
- Topics are **multi-subscriber**: many consumers/groups can read the same topic independently.

---

## 2. Partitions — the heart of Kafka

- Each topic is split into **N partitions** (`orders-0`, `orders-1`, …). You choose N at
  creation (and can increase it later).
- **Each partition is its own append-only log** with its own offsets.
- **Why partitions exist:**
  - **Parallelism** — different partitions live on different brokers and are consumed by
    different group members at the same time → horizontal scale.
  - **Ordering** — Kafka guarantees order **within a partition only**. There is **no global
    order** across partitions. This is the single most-tested fact.

---

## 3. Offsets

- An **offset** is the position of a record in a partition: 0, 1, 2, 3… monotonically increasing.
- Offsets are **per-partition** — `orders-0` and `orders-1` both have an offset 5, unrelated.
- A consumer's progress = **the offset it has committed** per partition (Lesson 04).
- Special positions: `earliest` (oldest retained) and `latest` (next new record).

---

## 4. Keys decide the partition

When a producer sends a record it may include a **key**:

- **Key present:** partition = `hash(key) % numPartitions`. So **all records with the same key
  go to the same partition** → they keep their relative order. *Example: key = `customerId`
  means one customer's events are always ordered.*
- **No key:** records are spread across partitions (sticky-batched round-robin) for even load,
  but you lose per-entity ordering.

**Trade-off to mention:** keying gives ordering but risks **hot partitions** if one key is very
busy (e.g. one huge customer).

---

## 5. Choosing the partition count

- **More partitions** = more parallelism + throughput, but more open files, more memory, longer
  leader-election/failover, and more end-to-end latency.
- You can **increase** partitions later, but doing so **breaks key→partition mapping** for
  existing keys (the hash now lands differently), reshuffling ordering. So size with headroom.
- Rule of thumb: pick partitions ≥ the max number of consumers you want in a group (a partition
  can be read by only **one** member of a group at a time — extras sit idle).

---

## 🎤 Say this in the interview

- *"A topic is split into partitions; each partition is an independent ordered log with its own
  offsets. Order is guaranteed **within a partition**, never across the topic."*
- *"The **key** decides the partition via hashing, so same-key records stay ordered together —
  at the risk of hot partitions."*
- *"Partition count caps consumer-group parallelism: one partition → at most one consumer per
  group, so extra consumers idle."*

➡️ **Next:** [03 — Producers](./03_Producers.md)
