# 06 — Storage, Retention & Log Compaction

## 🧠 The One Idea

**Each partition on disk is a row of fixed-size notebooks (segments); Kafka only ever writes in
the last one and throws away old notebooks when they expire.** Retention decides *when* old data
is deleted — either by **age/size** (delete policy) or by **keeping only the latest value per
key** (compaction). That second mode turns a topic into a durable key-value snapshot.

The common one-liner: **"Partitions are split into segments; retention is either time/size-based
delete or key-based compaction."**

---

## 1. Segments — how a partition is stored

- A partition log is split into **segment files** (e.g. 1 GB or by time). Only the **active
  segment** is being written; older segments are immutable and closed.
- Each segment has an **index** mapping offset → byte position, so a consumer can jump to any
  offset quickly without scanning.
- **Why segments?** Deletion/compaction works at segment granularity — you drop or rewrite whole
  files instead of editing a giant one.

---

## 2. Sequential writes + zero-copy = speed

- Kafka **appends sequentially** to the active segment — sequential disk I/O is dramatically
  faster than random I/O.
- It relies on the OS **page cache** rather than a complex in-app cache.
- On read it uses **zero-copy** (`sendfile`): data goes from page cache straight to the socket,
  skipping user space. This is a big reason for Kafka's throughput.

---

## 3. Retention — when does data get deleted?

`cleanup.policy=delete` (the default):

- **`retention.ms`** — delete segments older than this (e.g. 7 days). Most common knob.
- **`retention.bytes`** — cap total size per partition; delete oldest beyond it.
- Deletion is **per segment**, lazy, in the background. Consumers reading old data that gets
  deleted will jump forward (offset out of range → `auto.offset.reset`).

**Key point:** retention is independent of whether anyone consumed the data. Kafka is not a queue
that deletes on read — it deletes on **policy**.

---

## 4. Log compaction — keep the latest value per key

`cleanup.policy=compact`:

- Instead of deleting by age, Kafka **keeps at least the most recent record for each key** and
  garbage-collects older values for that key.
- Result: the topic becomes a **changelog / snapshot** — replay it and you get the current state
  of every key. Perfect for "current account balance per user", config, or **Kafka Streams**
  state-store backing topics (and `__consumer_offsets` itself is compacted).
- A record with a **null value** is a **tombstone** → marks the key for deletion; removed after
  `delete.retention.ms`.
- You can combine: `cleanup.policy=compact,delete`.

**delete vs compact in one line:** delete = "keep a **window of history**"; compact = "keep the
**latest state per key** forever."

---

## 🎤 Say this in the interview

- *"Each partition is stored as immutable **segments** with offset indexes; Kafka appends
  sequentially and uses page cache + **zero-copy** for throughput."*
- *"Retention is policy-based, not on-read: **delete** by `retention.ms`/`retention.bytes`, or
  **compact** to keep the latest value per key."*
- *"**Compaction** turns a topic into a changelog/snapshot — a null value is a **tombstone** that
  deletes a key. It's how state stores and `__consumer_offsets` work."*

➡️ **Next:** [07 — Replication, ISR & high availability](./07_Replication_And_HA.md)
