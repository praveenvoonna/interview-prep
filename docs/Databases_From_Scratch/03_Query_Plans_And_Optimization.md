# 03 — Query Plans & Optimization

## 🧠 The One Idea

**Before running your query, the database makes a plan — like choosing a driving route — and
`EXPLAIN` shows you that route.** A slow query is almost always the database picking a bad route
(scanning a whole table instead of using an index). Optimization is reading the plan and giving the
planner what it needs to choose well.

The one-liner: **"`EXPLAIN ANALYZE` shows the plan; a seq scan where you expected an index scan is
the usual culprit."**

---

## 1. Reading EXPLAIN

```sql
EXPLAIN ANALYZE
SELECT * FROM orders WHERE customer_id = 42;
```

Key things to spot:

- **Seq Scan** — reads every row (bad for selective queries; fine for tiny tables).
- **Index Scan / Index-Only Scan** — used an index (good).
- **rows** estimate vs actual — big mismatch = stale statistics.
- **cost** and **actual time** — where the time goes.

---

## 2. Scans vs seeks

- **Full/seq scan:** O(n) — read the whole table. Acceptable for small tables or low-selectivity
  filters.
- **Index seek:** O(log n) — jump to matching rows. Wanted for selective filters.

The fix for an unexpected seq scan is usually **an appropriate index** (Lesson 01) — or making the
predicate **sargable** (next).

---

## 3. Sargable predicates

A predicate is **sargable** (index-usable) if the indexed column is left untouched:

- ❌ `WHERE YEAR(created_at) = 2026` — function on the column → can't use the index.
- ✅ `WHERE created_at >= '2026-01-01' AND created_at < '2027-01-01'` — range on the raw column.
- ❌ leading wildcard `LIKE '%abc'` can't use a B-tree; ✅ `LIKE 'abc%'` can.

Rewriting to sargable form is one of the highest-impact, zero-cost optimizations.

---

## 4. Joins & their algorithms

- **Nested loop:** good when one side is tiny / indexed.
- **Hash join:** good for large unindexed equality joins.
- **Merge join:** good when both inputs are sorted.

The planner picks based on **statistics**; keep them fresh (`ANALYZE`). Join on **indexed** columns
and select only the columns you need (enables covering indexes).

---

## 5. Common optimizations

- **Add/adjust indexes** for hot filters, joins, sorts.
- **`SELECT` only needed columns** (less I/O; covering-index potential).
- **Update statistics** (`ANALYZE`) so estimates are accurate.
- **Pagination:** prefer **keyset pagination** (`WHERE id > last`) over large `OFFSET` (which scans
  and discards).
- **N+1 queries:** batch/join instead of querying in a loop — a classic app-level killer.

---

## 🎤 Say this in the interview

- *"I start with `EXPLAIN ANALYZE` — a seq scan where I expected an index scan, or estimated-vs-
  actual row mismatches from stale stats, points to the problem."*
- *"I make predicates sargable — no functions on indexed columns, no leading wildcards — so the
  index is usable."*
- *"For large pages I use keyset pagination instead of big OFFSETs, and I kill N+1 patterns by
  batching or joining."*

➡️ **Next:** [04 — MongoDB](./04_MongoDB.md)
