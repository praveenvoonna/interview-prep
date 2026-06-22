# 00 — Relational vs NoSQL

## 🧠 The One Idea

**A relational database is a spreadsheet with strict rules and cross-references; NoSQL is a pile of
flexible folders, each holding everything one thing needs.** SQL shines when data is highly
related and correctness matters; NoSQL shines when you have massive scale and *known* access
patterns. The choice is about **your queries and consistency needs**, not fashion.

The one-liner: **"SQL for complex relations and strong consistency; NoSQL for horizontal scale and
known access patterns."**

---

## 1. Relational (SQL)

- Data in **tables** of rows/columns with a **fixed schema**.
- **Relationships** via foreign keys; **joins** combine tables.
- **ACID transactions** (Lesson 02) guarantee correctness.
- Examples: PostgreSQL, MySQL, Oracle.
- Scales **vertically** easily; horizontal scaling (sharding) is harder.

---

## 2. NoSQL families

| Family | Shape | Example | Good for |
|---|---|---|---|
| Document | JSON-like docs | MongoDB | flexible/nested data |
| Key-value | key → blob | DynamoDB, Redis | simple, huge-scale lookups |
| Wide-column | rows with dynamic columns | Cassandra | time-series, write-heavy |
| Graph | nodes + edges | Neo4j | relationship-heavy queries |

Most NoSQL stores trade joins/transactions for **horizontal scalability** and flexible schemas.

---

## 3. Normalization vs denormalization

- **Normalized (SQL):** each fact stored once; no duplication; joins assemble data. Great for
  writes/consistency, costs read-time joins.
- **Denormalized (NoSQL):** duplicate data so one read returns everything needed. Fast reads, but
  updates must touch every copy.

NoSQL data modeling = **"store it the way you'll read it"** (model around access patterns).

---

## 4. How to choose

Ask:
- **Relationships?** Many → SQL. Self-contained docs → document store.
- **Scale?** Beyond one node's writes → NoSQL or a sharded SQL setup.
- **Consistency?** Must-be-correct (money) → SQL/ACID. Tolerates eventual → NoSQL.
- **Access patterns known & fixed?** → NoSQL (model for them). Ad-hoc queries? → SQL.

Many systems span both (e.g. MySQL/Oracle + MongoDB/DynamoDB), so frame it as **right tool per
workload**.

---

## 5. "NewSQL" & polyglot persistence

- **NewSQL** (CockroachDB, Spanner): SQL + ACID **and** horizontal scale via distributed
  consensus — the best-of-both trend.
- **Polyglot persistence:** real systems mix stores — Postgres for transactions, Redis for cache,
  Elasticsearch for search, Kafka for events. Pick per need.

---

## 🎤 Say this in the interview

- *"I choose by access patterns and consistency: SQL for complex relations and ACID, NoSQL for
  horizontal scale with known query patterns."*
- *"NoSQL modeling means storing data the way you read it — denormalize for fast reads, accepting
  that updates touch every copy."*
- *"Real systems are polyglot: Postgres for transactions, Redis for cache, Kafka for events — the
  right store per workload."*

➡️ **Next:** [01 — Indexes](./01_Indexes.md)
