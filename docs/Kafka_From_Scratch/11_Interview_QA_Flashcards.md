# 11 — Interview Q&A Flashcards

Rapid-fire. Cover the answer, say it aloud, then check. Read twice.

---

## Fundamentals

**Q: What is Kafka in one sentence?**
A distributed, durable, append-only commit log: producers append records to partitions;
consumers read by offset at their own pace; data is retained for replay.

**Q: Queue vs pub/sub — which is Kafka?**
Both. A **consumer group** gives queue semantics (one member per partition); **multiple groups**
give pub/sub (each group sees all records). Data isn't deleted on read.

**Q: Why is Kafka fast?**
Sequential disk appends, batching + compression, OS page cache, and **zero-copy** reads
(`sendfile`). Clients talk directly to partition leaders — no proxy hop.

---

## Topics, partitions, offsets

**Q: What's a partition and why does it exist?**
An independent ordered log within a topic. It's the unit of **parallelism** (spread across
brokers/consumers) and **ordering** (guaranteed only within a partition).

**Q: How is a record's partition chosen?**
By `hash(key) % partitions` if a key is set (same key → same partition → ordered); otherwise
sticky round-robin across partitions.

**Q: What's an offset?**
A record's position within a partition (per-partition, monotonically increasing). A consumer's
progress is its committed offset.

**Q: Gotcha of increasing partitions later?**
It changes key→partition mapping, so existing keys reshuffle and per-key ordering breaks. Size
ahead.

---

## Producers

**Q: Explain `acks`.**
`0` fire-and-forget (can lose), `1` leader-only (loses if leader dies pre-replication), `all`
waits for all in-sync replicas (durable). Use `all` + `min.insync.replicas=2`.

**Q: What does the idempotent producer do?**
Dedupes retries via producer id + per-partition sequence numbers → exactly-once writes per
partition, no duplicates from retries. Default on modern Kafka.

**Q: How to raise throughput?**
`linger.ms` + larger `batch.size` + compression (`zstd`/`lz4`).

---

## Consumers & groups

**Q: How does a consumer group work?**
Each partition is owned by exactly one member; partitions are split across members. Partition
count caps parallelism — extra consumers idle.

**Q: What's a rebalance and how to avoid bad ones?**
Reassignment of partitions when membership/partitions change. Avoid churn with
cooperative-sticky assignor and by processing within `max.poll.interval.ms` (lower
`max.poll.records`).

**Q: At-least vs at-most-once — how chosen?**
Where you commit: after processing → at-least-once (possible duplicates); before → at-most-once
(possible loss).

**Q: What is lag?**
`log-end-offset − committed-offset` — how far behind a consumer is. The key health metric.

---

## Delivery & exactly-once

**Q: How does exactly-once work?**
Idempotent producer (retry dedupe) + **transactions** that atomically commit output records and
consumer offsets; consumers read with `isolation.level=read_committed`.

**Q: Limit of Kafka EOS?**
It's exactly-once **within Kafka**. External DBs/APIs still need idempotent writes or an outbox.

---

## Storage & HA

**Q: delete vs compact retention?**
`delete` removes by age/size (a history window); `compact` keeps the latest value per key (a
changelog/snapshot). Null value = tombstone.

**Q: What's the ISR?**
In-sync replicas — replicas caught up with the leader. `acks=all` waits for the ISR; only ISR
members become leader (unless unclean election).

**Q: State the durability contract.**
RF=3 + `acks=all` + `min.insync.replicas=2`: survive one broker loss with no data loss;
under-replicated writes are rejected.

**Q: Unclean leader election?**
Promoting an out-of-sync replica when no ISR remains. `false` = consistency (no data loss),
`true` = availability (risk loss). Keep it off.

---

## Ecosystem

**Q: Connect vs Streams?**
**Connect** moves data in/out of external systems via config-driven connectors (Debezium for
CDC). **Streams** is a Java library for stateful transforms (joins/aggregations) backed by
compacted changelog state stores.

**Q: What's ZooKeeper's replacement?**
**KRaft** — Kafka manages its own metadata via Raft-based controllers; no ZooKeeper. The modern
default.

**Q: Schema Registry — why?**
Versions Avro/Protobuf/JSON schemas and enforces compatibility so events evolve without breaking
consumers.

---

🎉 **You finished Kafka From Scratch.** Re-read the three-sentence summary in the
[README](./README.md), then say the durability contract and the partition/ordering rule out
loud — those two answers carry the most weight.
