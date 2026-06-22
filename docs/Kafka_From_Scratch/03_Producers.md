# 03 — Producers

## 🧠 The One Idea

**A producer is a mail-room clerk that batches letters by destination, stamps them, and decides
how sure it needs to be that they arrived.** It groups records headed to the same partition into
one envelope (batch), can compress the envelope, and waits for a chosen level of acknowledgement
(`acks`) before calling it "delivered." Tune those three dials — **batching, compression,
acks** — and you trade latency for throughput and durability.

The common one-liner: **"Producers append records to partition leaders with a configurable
durability/throughput trade-off."**

---

## 1. The send path

1. You build a record: `topic`, optional `key`, `value`, optional headers.
2. The **partitioner** picks the partition (by key hash, or sticky round-robin if no key).
3. The record joins a **batch** buffered in memory per partition.
4. The batch is sent to that partition's **leader broker** when it's full or `linger.ms` elapses.
5. The leader writes it, replicates per `acks`, and returns metadata (partition + offset).

Sends are **asynchronous** — you get a future/callback; don't block on each one or throughput
collapses.

---

## 2. `acks` — the durability dial (know this cold)

- **`acks=0`** — fire and forget. Fastest, but data can be silently lost. Rarely used.
- **`acks=1`** — leader writes to its log and acks. Fast, but if the leader dies **before**
  followers copy the record, it's lost.
- **`acks=all` (= -1)** — leader waits until all **in-sync replicas (ISR)** have the record.
  Strongest durability. Pair with **`min.insync.replicas=2`** so a write fails if too few
  replicas are caught up (rather than silently accepting under-replicated data).

**Durability contract = `acks=all` + `min.insync.replicas≥2` + replication factor ≥3.**

---

## 3. Idempotent producer — no duplicates from retries

Retries (after a network hiccup) can write the **same** record twice. The **idempotent
producer** (`enable.idempotence=true`, the default in modern Kafka) gives each producer a PID
and per-partition sequence numbers, so the broker **dedupes retries**. Result: **exactly-once
*per partition* writes** even with retries. It also safely enables ordering with retries.

---

## 4. Batching & compression — the throughput dials

- **`batch.size`** — max bytes per batch per partition.
- **`linger.ms`** — how long to wait to fill a batch before sending (e.g. 5–20ms). A tiny linger
  hugely improves batching/throughput at a small latency cost.
- **`compression.type`** — `lz4`/`zstd`/`snappy`/`gzip`. Compresses the whole batch → less
  network and disk, often *higher* throughput. `zstd` and `lz4` are common picks.

Bigger batches + some linger + compression = far more messages/sec.

---

## 5. Ordering caveats

- Order is preserved **per partition**. With idempotence on, you can keep
  `max.in.flight.requests.per.connection` up to 5 and still preserve order.
- **Without** idempotence, more than 1 in-flight request + retries can **reorder** records.
- A key guarantees same-partition placement (Lesson 02), which is what makes ordering meaningful.

```java
props.put("acks", "all");
props.put("enable.idempotence", "true");
props.put("linger.ms", "10");
props.put("compression.type", "zstd");
producer.send(new ProducerRecord<>("orders", customerId, payload));
```

---

## 🎤 Say this in the interview

- *"Producers batch per-partition, optionally compress, and send to the leader. `acks` is the
  durability dial: 0/1/all — I use `acks=all` with `min.insync.replicas=2` for durable writes."*
- *"The **idempotent producer** dedupes retries via PID + sequence numbers, giving
  exactly-once writes per partition without duplicates."*
- *"`linger.ms` + `batch.size` + compression trade a little latency for big throughput gains."*

➡️ **Next:** [04 — Consumers & consumer groups](./04_Consumers_And_Groups.md)
