# 07 — Interview Q&A Flashcards

Rapid-fire. Cover the answer, say it aloud, then check. Read twice.

---

## SQL vs NoSQL

**Q: How do you choose?**
By access patterns + consistency: SQL for complex relations and ACID; NoSQL for horizontal scale
with known query patterns.

**Q: NoSQL modeling principle?**
Store data the way you read it — denormalize for fast reads; updates touch every copy.

---

## Indexes

**Q: What does an index do, and the cost?**
Turns a full scan (O(n)) into a seek (O(log n)); costs slower writes and storage.

**Q: Postgres index types?**
B-tree (equality+range, default), Hash (equality), GIN (JSONB/full-text/arrays), GiST (geo/ranges),
BRIN (huge time-ordered tables).

**Q: Composite index rule?**
Left-prefix: `(a,b)` helps `a` or `a,b`, not `b` alone.

**Q: Covering index?**
Index contains all columns the query needs → index-only scan, no table lookup.

**Q: When NOT to index?**
Low-selectivity columns, write-heavy tables, unused indexes.

---

## Transactions & ACID

**Q: ACID?**
Atomicity, Consistency, Isolation, Durability — all-or-nothing, valid state, isolated, survives
crashes.

**Q: The anomalies?**
Dirty read, non-repeatable read, phantom read, lost update.

**Q: Isolation levels?**
Read Uncommitted → Read Committed → Repeatable Read → Serializable (each prevents more, costs
concurrency).

**Q: What is MVCC?**
Readers see a consistent snapshot while writers create new versions → reads don't block writes.

**Q: Optimistic vs pessimistic?**
Optimistic = version-check at commit + retry (low contention); pessimistic = lock first (high
contention).

---

## Query optimization

**Q: How do you debug a slow query?**
`EXPLAIN ANALYZE` — look for seq scan vs index scan and estimated-vs-actual row mismatches (stale
stats).

**Q: Sargable predicate?**
Index-usable: no function on the indexed column, no leading wildcard.

**Q: Big OFFSET pagination problem?**
It scans and discards rows; use keyset pagination (`WHERE id > last`).

---

## MongoDB

**Q: Embed vs reference?**
Embed data read together and bounded (one read); reference large/unbounded/shared data (16MB doc
limit).

**Q: Aggregation pipeline order tip?**
`$match` first to filter early and use an index, then `$group`/`$sort`.

**Q: Atomicity scope?**
Single-document atomic; multi-doc transactions exist but cost more.

---

## DynamoDB

**Q: Design approach?**
Access-pattern-first: list queries, design keys so each is one Query — never Scan.

**Q: Partition key requirement?**
High cardinality for even spread; write-shard an unavoidable hot key.

**Q: GSI vs LSI?**
GSI = different PK/SK, added anytime; LSI = same PK, alternate SK, created with the table.

---

## Replication & sharding

**Q: Replication vs sharding?**
Replication = full copies (redundancy + read scale); sharding = subsets (write + storage scale).

**Q: Replication lag implication?**
Stale reads; route read-your-writes to the leader briefly.

**Q: Order of adoption?**
Replicate first; shard only when one node can't hold the writes/data.

---

🎉 **You finished Databases From Scratch.** Lead with the answer the feedback wanted: **index types
(B-tree/Hash/GIN/BRIN), the left-prefix rule, and when an index hurts** — then ACID isolation
levels. Those two topics carry most database interviews.
