# 06 — Replication & Sharding

## 🧠 The One Idea

**Replication is photocopying the whole filing cabinet (for safety and more readers); sharding is
splitting the files across many cabinets (so no one cabinet is overwhelmed).** They solve different
problems — copies vs splits — and real systems use **both**: shard for write/storage scale,
replicate each shard for safety and read scale.

The one-liner: **"Replicate for redundancy + read scale; shard for write + storage scale; combine
both at large scale."**

---

## 1. Replication

- **Leader–follower:** writes to the leader, reads from followers.
- **Sync vs async:**
  - **Async** — leader doesn't wait for followers → fast, but followers **lag** (stale reads,
    possible loss on failover).
  - **Sync** — wait for followers → no loss, slower writes.
- **Multi-leader / leaderless** (Dynamo/Cassandra): accept writes anywhere → high availability,
  but **conflict resolution** needed (last-write-wins, vector clocks).

---

## 2. What replication buys (and costs)

- ✅ **HA / failover:** promote a follower if the leader dies.
- ✅ **Read scaling:** spread reads across replicas.
- ⚠️ **Replication lag:** followers can serve stale data → "read-your-writes" needs care (route a
  user's reads to the leader briefly after they write).
- Every replica holds the **full** dataset — doesn't help write or storage limits.

---

## 3. Sharding (partitioning)

Split data so each node holds a **subset** (recap from System Design, applied to DBs):

- **Range:** by key range → range-query friendly, risks hot ranges.
- **Hash:** `hash(key)` → even spread, range queries hard.
- **Consistent hashing:** minimizes data movement when adding/removing nodes.

The **shard key** decision is paramount: even distribution + query locality, hard to change later.

---

## 4. The costs of sharding

- **Cross-shard queries** → scatter-gather (slow); avoid by colocating related data.
- **Cross-shard transactions** → 2-phase commit or sagas (complex).
- **Rebalancing** when capacity changes — consistent hashing limits the churn.
- **Operational overhead** — more nodes to monitor/back up.

So: **replicate first** (simpler, big wins), **shard only when one node can't hold the writes or
data**.

---

## 5. Putting it together

```
                 ┌── Shard A (leader + 2 followers)
client → router ─┼── Shard B (leader + 2 followers)
                 └── Shard C (leader + 2 followers)
```

Each shard owns a key range/hash bucket; within a shard, replication gives HA + read scale. This is
how large databases (and distributed datastores generally) scale both dimensions at once.

---

## 🎤 Say this in the interview

- *"Replication gives redundancy and read scaling via leader–follower, but async replicas lag, so
  read-your-writes needs leader reads briefly after a write."*
- *"Sharding scales writes and storage by partitioning on a key — hash for even spread, range for
  range queries — and the shard key is hard to change, so I pick it carefully."*
- *"I replicate before sharding because cross-shard joins and transactions are expensive; at scale
  I shard and replicate each shard."*

➡️ **Next:** [07 — Interview Q&A flashcards](./07_Interview_QA_Flashcards.md)
