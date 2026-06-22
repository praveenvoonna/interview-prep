# 08 — Worked Examples

Three designs, each walked through the 6-step framework (Lesson 00). Practise *narrating* these.

---

## Example 1 — Distributed rate limiter

**Requirements:** cap each user/API key to N requests/sec across many app servers; low latency;
must be shared (not per-instance).

**Approach:**
- **Algorithm:** **token bucket** — refill N tokens/sec; each request takes one; empty = reject.
  (Allows bursts; sliding-window-counter is the stricter alternative.)
- **Shared state:** store counters in **Redis** keyed by `user:window`; use atomic
  `INCR`+`EXPIRE` (or a Lua script) so all servers share one count.
- **Scale:** Redis handles huge QPS; shard by key if needed. Local pre-check to cut Redis load.
- **Failure:** if Redis is down, **fail open** (allow) or degrade — decide per business risk.

**Trade-off line:** *"Token bucket allows bursts; I keep counters in Redis with atomic ops so the
limit is global, and fail open if the store is unavailable."*

---

## Example 2 — Metrics ingestion pipeline (your wheelhouse)

**Requirements:** millions of metric points/sec from many agents; queryable dashboards; tolerate
spikes; some loss acceptable.

**Approach:**
- **Ingest:** agents → load balancer → stateless collectors.
- **Buffer:** collectors publish to **Kafka** (partitions = parallelism; absorbs spikes — your
  Kafka course).
- **Process:** consumer groups aggregate/downsample → write to a **time-series DB** (Prometheus/
  TSDB).
- **Serve:** query layer + cache for dashboards.
- **Scale/failure:** Kafka retention replays after a consumer outage; at-least-once + idempotent
  writes.

**Trade-off line:** *"Kafka decouples ingest from processing and absorbs bursts; I accept
eventual consistency and make writes idempotent for at-least-once delivery."*

---

## Example 3 — URL shortener (the classic)

**Requirements:** create short→long mappings; billions of reads, far fewer writes; redirects must
be fast.

**Approach:**
- **API:** `POST /shorten` → returns code; `GET /{code}` → 301 redirect.
- **ID generation:** base-62 encode an ID; use a counter/snowflake or hash with collision check.
- **Data:** key-value store (`code → URL`) — read-heavy, so **cache hot codes** + CDN.
- **Scale:** reads dominate → heavy caching + read replicas; shard by code if storage grows.
- **Failure:** cache-aside with TTL; negative caching for unknown codes.

**Trade-off line:** *"It's read-dominated, so I optimise the redirect path with aggressive caching
and replicas, and shard the KV store by code only when storage demands it."*

---

## How to use these
For any prompt: **state requirements + estimate, draw boxes (client→LB→service→store→queue), then
attack scale & failure with caching/sharding/replication and resilience patterns.** Always end
each choice with *"the trade-off is…"*.

➡️ **Next:** [09 — Interview Q&A flashcards](./09_Interview_QA_Flashcards.md)
