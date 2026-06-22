# 06 — Databases: RDS, DynamoDB & Friends

## 🧠 The One Idea

**AWS runs the database for you so you stop babysitting backups and patches — and the big choice
is relational (structured, joins, SQL) vs NoSQL (flexible, massive scale).** RDS is the managed
SQL family; DynamoDB is the serverless key-value/NoSQL workhorse. Pick by your **data shape and
access pattern**, not by habit.

The common one-liner: **"RDS = managed relational; DynamoDB = serverless NoSQL at any scale."**

---

## 1. RDS — managed relational databases

- Runs engines you know: **PostgreSQL, MySQL, MariaDB, Oracle, SQL Server**. AWS handles
  **provisioning, patching, backups, and failover** — you keep schema/queries.
- **Multi-AZ** = a **standby replica in another AZ** for **high availability** (auto-failover on
  outage). It's for resilience, **not** read scaling.
- **Read replicas** = async copies to **scale reads** (and can be promoted). Different purpose
  from Multi-AZ.
- **Use when:** structured data, relationships/**joins**, transactions, strong consistency
  (classic OLTP apps).

### Aurora
- AWS's cloud-native MySQL/PostgreSQL-compatible engine: faster, storage auto-grows, 6 copies
  across 3 AZs. **Aurora Serverless** auto-scales capacity. Mention as the "RDS, but
  higher-performance/managed-further" option.

---

## 2. DynamoDB — serverless NoSQL

- Fully managed **key-value / document** store with **single-digit-millisecond** latency at **any
  scale**. No servers, no patching; scales horizontally automatically.
- **Data model:** tables with a **partition key** (and optional sort key). Design around your
  **access patterns** — query by key, not ad-hoc joins.
- **Capacity:** **on-demand** (pay per request) or **provisioned** (set RCUs/WCUs).
- **Features:** **Global Tables** (multi-Region active-active), **DynamoDB Streams** (change feed
  → Lambda), **TTL** auto-expiry, **DAX** in-memory cache.
- **Use when:** huge scale, simple/known query patterns, key-based lookups, serverless apps.
  **Avoid when** you need complex joins/ad-hoc queries → use relational.

---

## 3. Caching & in-memory

- **ElastiCache** (**Redis**/Memcached) — microsecond in-memory cache in front of a DB to cut
  latency and load. Redis also does pub/sub, sorted sets, leaderboards.
- Pattern: **cache-aside** — check cache → miss → read DB → populate cache.

---

## 4. Purpose-built databases (name-drop)

- **Redshift** — petabyte-scale **data warehouse** for analytics/OLAP (columnar).
- **OpenSearch** — search & log analytics.
- **Neptune** (graph), **DocumentDB** (Mongo-compatible), **Timestream** (time-series),
  **Athena** (serverless SQL **directly over S3**).

**One-liner:** *"AWS has a purpose-built database per workload — I pick by access pattern, not one
DB for everything."*

---

## 5. Choosing (decision table)

| Need | Use |
|---|---|
| Relational, joins, transactions (OLTP) | **RDS / Aurora** |
| Key-based lookups at massive scale, serverless | **DynamoDB** |
| Cut latency / offload reads | **ElastiCache (Redis)** |
| Analytics over huge datasets | **Redshift** (or **Athena** over S3) |

---

## 🎤 Say this in the interview

- *"**RDS** is managed relational (Postgres/MySQL/…); **Multi-AZ** is for HA failover, **read
  replicas** are for read scaling — different jobs."*
- *"**DynamoDB** is serverless NoSQL with single-digit-ms latency; I model around access patterns
  with a partition key, and use Streams/Global Tables/TTL."*
- *"I add **ElastiCache (Redis)** for hot reads and pick **purpose-built** DBs — Redshift for
  warehousing, Athena for SQL over S3 — instead of forcing one database."*

➡️ **Next:** [07 — Scaling & load balancing](./07_Scaling_And_LB.md)
