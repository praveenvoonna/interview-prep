# 03 — Databases & Sharding

## 🧠 The One Idea

**When one filing cabinet overflows, you buy more cabinets and agree on a rule for which letters
go where (A–M here, N–Z there).** That rule is **sharding** — splitting data across machines so no
single one holds (or serves) it all. The trick is picking a **shard key** that spreads load evenly
and keeps related data together.

The one-liner: **"Replication scales reads and adds redundancy; sharding (partitioning by a key)
scales writes and storage."**

---

## 1. SQL vs NoSQL (the choice)

| | SQL (relational) | NoSQL |
|---|---|---|
| Schema | fixed, normalised | flexible |
| Joins / transactions | strong | limited |
| Scale | vertical / harder horizontal | built for horizontal |
| Use when | complex relations, strong consistency | huge scale, known access patterns |

Pick based on **access patterns and consistency needs**, not hype (see Databases course).

---

## 2. Replication (scale reads + HA)

- **Leader–follower:** writes go to the leader, reads fan out to followers.
- **Async replication:** fast, but followers lag → **stale reads** (eventual consistency).
- **Sync replication:** no lag, slower writes.
- **Failover:** promote a follower if the leader dies (ties to consensus, Lesson 06).

Replication gives **read scaling + redundancy**, but every replica holds the *full* dataset.

---

## 3. Sharding (scale writes + storage)

Split data across nodes; each shard holds a **subset**:

- **Range sharding:** by key range (A–M, N–Z). Simple, but risks **hot ranges**.
- **Hash sharding:** `hash(key) % N` → even spread, but range queries get hard.
- **Consistent hashing:** minimises reshuffling when nodes are added/removed (Lesson on hashing).
- **Directory/lookup:** a service maps key → shard (flexible, extra hop).

---

## 4. Choosing a shard key

The make-or-break decision:

- **Even distribution** — avoid hot shards (don't shard by `country` if 80% are one country).
- **Query locality** — related data on the same shard avoids cross-shard joins.
- **Avoid resharding** — changing the key later is painful; pick with headroom.

This mirrors Kafka's "key → partition" trade-off: ordering/locality vs hot partitions.

---

## 5. The costs of sharding

- **Cross-shard queries/joins** become expensive (scatter-gather).
- **Cross-shard transactions** need 2-phase commit or saga patterns.
- **Rebalancing** when adding shards — consistent hashing limits the damage.

So: **replicate first** (simpler), **shard only when one node can't hold the writes/data**.

---

## 🎤 Say this in the interview

- *"Replication scales reads and gives redundancy via leader–follower; async replication means
  followers can serve slightly stale reads."*
- *"Sharding scales writes by partitioning on a key — hash for even spread, range for range
  queries — and the shard key must avoid hot shards while keeping query locality."*
- *"I'd replicate before sharding, since cross-shard joins and transactions are costly; consistent
  hashing limits reshuffling when I add nodes."*

➡️ **Next:** [04 — Queues & async](./04_Queues_And_Async.md)
