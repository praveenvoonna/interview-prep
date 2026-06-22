# 04 — Queues & Async

## 🧠 The One Idea

**A message queue is a restaurant order rail: the waiter clips the order and walks away; the
kitchen cooks at its own pace.** Instead of the caller waiting for slow work, you **drop a message
and move on**. This decouples producer from consumer, smooths traffic spikes, and lets each side
scale and fail independently.

The one-liner: **"Async queues decouple producers from consumers, absorb spikes, and let work
retry — at the cost of eventual consistency."**

---

## 1. Why go async

- **Decoupling:** producer doesn't know/wait for the consumer.
- **Spike absorption (buffering):** a burst fills the queue; consumers drain steadily.
- **Resilience:** if a consumer is down, messages wait — no data lost.
- **Parallelism:** many consumers process the queue concurrently.

Classic uses: sending email, image processing, order pipelines, analytics ingestion.

---

## 2. Queue vs log (and Kafka)

- **Message queue (SQS/RabbitMQ):** a message is consumed and **removed**; good for task
  distribution.
- **Log (Kafka):** messages are **retained** and read by offset; multiple independent consumers
  can replay. (See your Kafka course — partitions = parallelism + ordering.)

Pick a **queue** for "do this task once"; a **log** for "many systems react to this event / replay".

---

## 3. Delivery guarantees

- **At-most-once:** may lose, never duplicates.
- **At-least-once:** never loses, **may duplicate** → consumers must be **idempotent**.
- **Exactly-once:** hardest; usually at-least-once + idempotency/dedup keys (Kafka transactions
  give it *within* Kafka).

Say: *"I design consumers to be idempotent so at-least-once delivery is safe."*

---

## 4. Backpressure & failure handling

- **Backpressure:** when consumers can't keep up, the queue grows — monitor **lag** and
  autoscale consumers, or shed/throttle producers.
- **Retries with backoff:** transient failures retry with exponential backoff + jitter.
- **Dead-letter queue (DLQ):** messages that keep failing go to a DLQ for inspection instead of
  blocking the pipeline.

---

## 5. Patterns

- **Work queue:** N workers pull tasks → horizontal task scaling.
- **Pub/sub (fan-out):** one event, many subscribers react.
- **Outbox pattern:** write the DB row and the "to-send" message in **one transaction**, then a
  relay publishes it — solves the dual-write problem (DB + queue consistency).
- **Saga:** coordinate a multi-service transaction via a chain of events + compensating actions.

---

## 🎤 Say this in the interview

- *"I'll make this async with a queue to decouple producer and consumer, absorb spikes, and retry
  on failure — accepting eventual consistency."*
- *"Delivery is at-least-once, so I make consumers idempotent with a dedup key; persistent
  failures go to a dead-letter queue."*
- *"To avoid the dual-write problem between DB and queue, I'd use the outbox pattern — write the
  row and the message in one transaction, then relay it."*

➡️ **Next:** [05 — Consistency & CAP](./05_Consistency_And_CAP.md)
