# 09 — Operations, Performance & Gotchas

## 🧠 The One Idea

**Most Kafka pain comes from three places: too few/too many partitions, consumers that process
too slowly, and weak durability settings.** Know the handful of metrics and configs that govern
those, and you can reason about almost any production incident.

The common one-liner: **"Watch lag, ISR, and rebalances; tune partitions, batching, and
durability."**

---

## 1. The metrics that matter

- **Consumer lag** — `end offset − committed offset`. The #1 health signal. Rising lag = can't
  keep up.
- **Under-replicated partitions** — partitions where ISR < RF. Should normally be **0**;
  non-zero means a broker is slow/down → durability at risk.
- **Offline partitions** — partitions with no leader → outage. Must be 0.
- **Rebalance rate** — frequent rebalances stall consumption.
- **Request latency / throughput**, **broker disk & network**, **page cache** usage.

---

## 2. Common gotchas (the ones interviewers love)

- **"Messages out of order!"** — order is only **per partition**. Multi-partition or no key →
  no global order. Fix: key by the entity that needs ordering.
- **"Duplicates!"** — at-least-once + a crash before commit. Fix: idempotent processing or EOS.
- **"Constant rebalances!"** — processing a poll batch takes longer than
  `max.poll.interval.ms`, so the member is kicked. Fix: smaller `max.poll.records`, faster
  processing, or longer interval; use **cooperative-sticky** assignor.
- **"Hot partition"** — one key dominates traffic → one partition/consumer overloaded. Fix:
  better key, or a composite key.
- **"Data loss on broker failure"** — `acks=1` or `min.insync.replicas=1`. Fix: `acks=all` +
  RF=3 + `min.insync.replicas=2`.
- **"Adding partitions broke ordering"** — key→partition hash changed. Plan partition count
  ahead.
- **Large messages** — Kafka is tuned for small records; for big blobs store in S3 and send a
  pointer (the claim-check pattern).

---

## 3. Performance tuning levers

- **Producer:** `linger.ms` (5–20), `batch.size` up, `compression.type=zstd/lz4`,
  `acks` per durability need.
- **Consumer:** enough members (≤ #partitions), `fetch.min.bytes`/`fetch.max.wait.ms` for
  batching, `max.poll.records` sized so a batch processes within `max.poll.interval.ms`.
- **Topic:** partition count for target parallelism; right retention/compaction.
- **Broker:** fast disks (sequential), plenty of RAM for **page cache**, good network; spread
  leaders evenly.

---

## 4. Operational basics

- **Create/inspect topics:** `kafka-topics --create/--describe`.
- **Check groups & lag:** `kafka-consumer-groups --describe --group <g>`.
- **Reassign partitions / rebalance leaders** when adding brokers.
- **Rolling restarts** for upgrades — one broker at a time, wait for ISR to recover (0
  under-replicated) before the next.
- **Quotas** to stop a noisy client starving others.

---

## 5. Sizing rules of thumb

- Partitions ≈ `max(target throughput / per-partition throughput, desired consumer parallelism)`,
  with headroom — but avoid *tens of thousands* per broker (failover/memory cost).
- **RF=3** for production durability; **min.insync.replicas=2**.
- Keep some partitions spare so you can scale consumers without re-partitioning.

---

## 🎤 Say this in the interview

- *"My first three dashboards: **consumer lag**, **under-replicated partitions**, and
  **rebalance rate** — they catch most incidents."*
- *"Constant rebalances usually mean processing exceeds `max.poll.interval.ms`; I lower
  `max.poll.records` or speed up the handler and use the cooperative-sticky assignor."*
- *"For durability I standardize on **RF=3, acks=all, min.insync.replicas=2**, and I size
  partitions ahead because increasing them later reshuffles key ordering."*

➡️ **Next:** [10 — Hands-on lab](./10_Hands_On_Lab.md)
