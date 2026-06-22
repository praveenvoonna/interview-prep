# 02 — Caching

## 🧠 The One Idea

**A cache is keeping your most-used tools on the desk instead of walking to the storeroom every
time.** Reading from memory (or a nearby Redis) is orders of magnitude faster than hitting the
database. The whole art is **what to keep on the desk, and how to notice when the storeroom copy
changed** — because a stale cached value is worse than no cache.

The one-liner: **"Cache to serve hot reads from memory; the hard part is invalidation and
staleness."**

---

## 1. Where caches live

- **Client / browser** — avoid the request entirely.
- **CDN** — static assets at the edge, near users.
- **Application / in-process** — fastest, but per-instance and not shared.
- **Distributed cache (Redis/Memcached)** — shared across instances; the workhorse.
- **Database cache** — query/buffer cache inside the DB.

The closer to the user, the faster — and the harder to invalidate.

---

## 2. Caching strategies

- **Cache-aside (lazy):** app checks cache; on miss, reads DB and populates cache. Most common.
- **Read-through:** the cache library loads from DB on miss transparently.
- **Write-through:** write to cache **and** DB synchronously → consistent, slower writes.
- **Write-back (write-behind):** write to cache, flush to DB later → fast writes, risk of loss.

Name cache-aside as your default and mention the others as trade-offs.

---

## 3. Invalidation — "the hardest problem"

You keep a cached copy fresh by:

- **TTL (expiry):** simplest — accept staleness up to the TTL.
- **Explicit invalidation:** delete/update the key on write.
- **Versioning:** include a version in the key so old entries fall out.

> "There are only two hard things in CS: cache invalidation and naming things." Quote it, then
> show you handle it with TTLs + write-time invalidation.

---

## 4. Eviction policies

When the cache is full, what goes?

- **LRU** (least-recently-used) — the common default.
- **LFU** (least-frequently-used), **FIFO**, **TTL-based**.

Match the policy to the access pattern; LRU fits most "recent is hot" workloads.

---

## 5. The classic failure modes

- **Thundering herd / cache stampede:** a hot key expires and thousands of requests hit the DB at
  once. Fix: request coalescing, jittered TTLs, or a lock to repopulate once.
- **Hot key:** one key gets disproportionate traffic. Fix: replicate the key / local cache it.
- **Cache penetration:** repeated misses for nonexistent keys hit the DB. Fix: cache the negative
  result (a "miss marker").

These named failure modes are exactly the depth interviewers probe.

---

## 🎤 Say this in the interview

- *"I'll cache hot reads in Redis with cache-aside; on miss I read the DB and populate, with a TTL
  to bound staleness."*
- *"Invalidation is the hard part — I combine TTLs with write-time invalidation, and version keys
  when needed."*
- *"To avoid a stampede on a hot key expiry I'd jitter TTLs and coalesce requests so only one
  rebuild hits the DB."*

➡️ **Next:** [03 — Databases & sharding](./03_Databases_And_Sharding.md)
