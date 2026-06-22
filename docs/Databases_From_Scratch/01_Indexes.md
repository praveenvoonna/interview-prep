# 01 — Indexes

## 🧠 The One Idea

**An index is the alphabetical thumb-tabs on a dictionary: instead of reading every page to find
"zebra", you jump straight there.** Without an index the DB does a **full table scan** (read every
row). With one it does a **seek** (go straight to the rows). The cost: every index must be kept up
to date on writes, and it takes storage.

> *Index types are one of the most common database interview topics — know them cold.*

The one-liner: **"An index turns an O(n) full scan into an O(log n) seek — at the cost of slower
writes and extra storage."**

---

## 1. The B-tree index (the default)

- Most relational indexes are **B-trees** (balanced trees): sorted, O(log n) lookup.
- Great for **equality** (`=`), **ranges** (`<`, `>`, `BETWEEN`), **prefix** `LIKE 'abc%'`, and
  **ORDER BY** (the data is already sorted).
- This is what you get by default with `CREATE INDEX` in Postgres/MySQL.

---

## 2. Other index types (Postgres vocabulary)

| Type | Best for |
|---|---|
| **B-tree** | equality + range, ordering (default) |
| **Hash** | equality only (`=`), no ranges |
| **GIN** | "contains" — arrays, JSONB, full-text search |
| **GiST** | geometric / nearest-neighbour / ranges |
| **BRIN** | huge, naturally-ordered tables (time-series) — tiny index |

Naming **GIN for JSONB/full-text** and **BRIN for time-series** is exactly the depth the feedback
wanted.

---

## 3. Composite & covering indexes

- **Composite index** `(a, b, c)`: follows the **left-prefix rule** — usable for filters on `a`,
  `a,b`, or `a,b,c`, but **not** on `b` alone. Column order matters.
- **Covering index:** if the index contains *all* columns a query needs, the DB answers from the
  index alone — an **index-only scan**, no table lookup.

---

## 4. When an index HURTS

- **Writes:** every INSERT/UPDATE/DELETE must update every affected index → slower writes.
- **Storage:** indexes can rival table size.
- **Low selectivity:** indexing a column with few distinct values (e.g. boolean) is often useless —
  the planner just scans.
- **Over-indexing:** many unused indexes all cost writes for no read benefit.

Rule: index columns you **filter/join/sort on frequently and selectively**; drop unused ones.

---

## 5. Clustered vs secondary; reading the plan

- **Clustered index** (MySQL InnoDB primary key): the table rows are physically stored in index
  order — one per table.
- **Secondary index:** points back to the row (via primary key).
- **Check it works:** `EXPLAIN ANALYZE` (Lesson 03) — look for **Index Scan** vs **Seq Scan**.

---

## 🎤 Say this in the interview

- *"A B-tree index gives O(log n) seeks for equality and range queries; it's the default. Hash is
  equality-only, GIN suits JSONB/full-text, BRIN suits huge time-ordered tables."*
- *"Composite indexes follow the left-prefix rule, so `(a,b)` helps filters on `a` or `a,b` but not
  `b` alone; a covering index enables index-only scans."*
- *"Indexes slow writes and cost storage, and low-selectivity columns aren't worth indexing — I
  verify with EXPLAIN that I'm getting an index scan, not a seq scan."*

➡️ **Next:** [02 — Transactions & ACID](./02_Transactions_And_ACID.md)
