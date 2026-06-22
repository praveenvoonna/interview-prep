# 00 — What is Kafka & Why It Exists

## 🧠 The One Idea

**Kafka is a giant, shared, append-only notebook (a "log") that many writers add lines to and
many readers read from — at their own pace.** Writers never wait for readers; readers never
block writers. Each reader just remembers *which line number they've read up to* and continues
from there. That single idea — **a durable, replayable log everyone shares** — is the whole
product.

The common one-liner: **"Kafka is a distributed event streaming platform."** This lesson
unpacks what that means.

---

## 1. First, what's "the log"?

- A **log** here is not text logging — it's an **append-only sequence of records**, like a
  bank ledger. You only ever **add to the end**; you never edit the middle.
- Every record gets a permanent, increasing **offset** (line number): 0, 1, 2, 3…
- Readers track their own offset. Reader A can be at offset 100 while reader B is at offset 5 —
  **they don't interfere.**

This is why Kafka is so powerful: the data **stays** after being read (until retention expires),
so you can **replay** it, add **new** consumers later, and reprocess history.

---

## 2. The problem Kafka solves

Imagine many systems that need to share data: a website emits "user clicked", and orders,
analytics, fraud-detection, email, and a data warehouse all need those events.

**Without Kafka (point-to-point spaghetti):** every producer wires directly to every consumer.
N producers × M consumers = an unmaintainable mesh; one slow consumer can stall a producer.

**With Kafka (a central hub):** producers write events **once** to Kafka; any number of
consumers read independently. Adding a new consumer doesn't touch the producer. Kafka becomes
the **central nervous system** for data in motion.

---

## 3. Pub/Sub vs queues — Kafka does both

- A **traditional message queue** (e.g. classic RabbitMQ) usually **deletes a message once
  consumed** by one worker. Good for task distribution, bad for replay/multiple readers.
- **Pub/Sub** broadcasts each message to **all** subscribers.
- **Kafka unifies them:** messages are **retained** (not deleted on read). A **consumer group**
  gives you queue-like load-balancing (each record processed by one member of the group), while
  **multiple groups** give you pub/sub (every group sees all records). One log, both patterns.

---

## 4. The core vocabulary (just enough for now)

| Term | Plain meaning |
|---|---|
| **Record / event / message** | One item: a key, a value (bytes), a timestamp, headers. |
| **Topic** | A named log — a category of events, e.g. `orders`. |
| **Partition** | A topic is split into partitions; each is an independent ordered log. |
| **Offset** | The position of a record within a partition. |
| **Producer** | Writes records to topics. |
| **Consumer** | Reads records from topics. |
| **Broker** | A Kafka server that stores partitions and serves clients. |
| **Cluster** | A set of brokers working together. |

---

## 5. Why people pick Kafka

- **High throughput** — millions of messages/sec by writing sequentially to disk and batching.
- **Durability** — data is persisted and **replicated** across brokers.
- **Scalability** — add partitions and brokers to scale horizontally.
- **Replayability** — consumers can rewind to any offset and reprocess.
- **Decoupling** — producers and consumers don't know about each other.

**When *not* to use it:** simple request/response RPC, tiny apps with one producer/consumer, or
when you need per-message priority and complex routing (a broker like RabbitMQ may fit better).

---

## 🎤 Say this in the interview

- *"Kafka is a distributed, durable, append-only commit log. Producers append records to
  partitions; consumers read by offset at their own pace, and data is retained for replay."*
- *"It decouples producers from consumers — write once, many independent readers — which is
  why it's used as the central event backbone."*
- *"Consumer groups give queue semantics (one member per partition); multiple groups give
  pub/sub (everyone sees everything)."*

➡️ **Next:** [01 — Architecture big picture](./01_Architecture_Big_Picture.md)
