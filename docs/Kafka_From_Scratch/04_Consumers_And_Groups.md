# 04 — Consumers & Consumer Groups

## 🧠 The One Idea

**A consumer group is a team splitting a stack of drawers (partitions) so no two teammates open
the same drawer at once.** Add a teammate and the drawers get redistributed (a **rebalance**);
lose one and their drawers go to the others. Each teammate writes a bookmark (**committed
offset**) saying how far they've read, so if they leave, whoever takes over resumes from the
bookmark — not from the start.

The common one-liner: **"A consumer group load-balances a topic's partitions across its
members, each member owning whole partitions."**

---

## 1. Consumer groups

- Every consumer declares a **`group.id`**. Consumers sharing a group.id form one **group**.
- Kafka assigns each partition to **exactly one** member of the group → work is split, no
  double-processing within the group.
- **Multiple groups** on the same topic each get the **full** stream (that's pub/sub).
- **Key rule:** a partition is owned by **one** member at a time. So **#partitions caps group
  parallelism** — more consumers than partitions means the extras sit **idle**.

---

## 2. Rebalancing

When membership or partition count changes (a consumer joins/leaves/crashes, or partitions are
added), the group **rebalances** — reassigns partitions to members.

- Coordinated by the broker **group coordinator**; an assignor (e.g. **cooperative-sticky**)
  decides the mapping.
- **Old "eager" rebalance** = stop-the-world: everyone revokes everything, then reassigns
  (pauses processing). **Cooperative rebalancing** moves only the partitions that need to move →
  less disruption. Prefer it.
- Frequent rebalances are a classic pain; caused by slow processing tripping `max.poll.interval.ms`
  or short session timeouts (Lesson 09).

---

## 3. Offsets & committing — where "delivery semantics" begin

- A consumer tracks the **offset** it has processed per partition and **commits** it (stored in
  the internal `__consumer_offsets` topic).
- **Auto-commit** (`enable.auto.commit=true`) commits periodically in the background — easy, but
  can drop or duplicate records around crashes.
- **Manual commit** (`commitSync`/`commitAsync`) lets you commit **after** you've successfully
  processed → control over semantics.
  - **Commit after processing** → **at-least-once** (a crash after processing but before commit
    reprocesses → duplicates possible).
  - **Commit before processing** → **at-most-once** (a crash after commit loses the record).
- On startup with no committed offset, **`auto.offset.reset`** decides: `earliest` or `latest`.

---

## 4. The poll loop

```java
consumer.subscribe(List.of("orders"));
while (running) {
  var records = consumer.poll(Duration.ofMillis(500));
  for (var r : records) process(r);   // do the work
  consumer.commitSync();              // then record progress → at-least-once
}
```

`poll()` does double duty: it **fetches records** *and* **sends heartbeats**. If you don't poll
often enough (slow processing), the group thinks you're dead and rebalances your partitions away.

---

## 5. Consumer lag

- **Lag** = `log-end-offset − committed-offset` for a partition: how far behind the consumer is.
- It's *the* health metric for a consumer. Growing lag = consumers can't keep up → add
  consumers (up to #partitions), speed up processing, or add partitions.
- Inspect with `kafka-consumer-groups --describe --group <g>`.

---

## 🎤 Say this in the interview

- *"A consumer group splits partitions across members — one partition per member at a time — so
  partition count caps parallelism. Multiple groups each get the whole stream (pub/sub)."*
- *"Where you commit offsets defines semantics: commit **after** processing → at-least-once;
  **before** → at-most-once. I prefer manual commit after processing plus idempotent handlers."*
- *"**Lag** (end offset − committed offset) is the key health metric; **cooperative rebalancing**
  avoids stop-the-world pauses."*

➡️ **Next:** [05 — Delivery semantics & exactly-once](./05_Delivery_Semantics.md)
