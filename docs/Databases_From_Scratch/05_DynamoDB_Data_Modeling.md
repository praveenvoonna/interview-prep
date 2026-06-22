# 05 — DynamoDB Data Modeling

## 🧠 The One Idea

**DynamoDB is a giant, infinitely-scalable hash table — but only fast if you know the exact key you
want.** You don't design tables around entities and then query freely (that's SQL). You design
around your **access patterns first**, then build keys so every query is a direct key lookup. Get
the key right and it scales to any size at single-digit-millisecond latency; get it wrong and you
fight it forever.

The one-liner: **"In DynamoDB you model access patterns first; the partition key must spread load
evenly, and you query by key — never scan."**

---

## 1. Keys: partition + sort

- **Partition key (PK):** hashed to decide which physical partition stores the item. Determines
  **distribution**.
- **Sort key (SK):** orders items *within* a partition → enables range queries (`begins_with`,
  `between`) inside one PK.
- **PK alone** = pure key-value; **PK + SK** = key-value with sorted ranges (the powerful mode).

---

## 2. Partition key = even distribution

- All items with the same PK live together → a **hot partition** if one PK gets disproportionate
  traffic (the #1 DynamoDB mistake).
- Choose a **high-cardinality** PK (e.g. `userId`, not `country`).
- If one key is unavoidably hot, **write-shard** it (`userId#1..N`) and fan-out reads.

This is the same hot-key lesson as Kafka partitions and DB sharding.

---

## 3. Access-pattern-first design

The DynamoDB workflow (opposite of SQL):

1. List **every query** the app needs.
2. Design keys/indexes so each query is **one** efficient `GetItem`/`Query`.
3. Only then create the table.

You **never** start from normalized entities — you start from "how will I read this?".

---

## 4. Single-table design & GSIs

- **Single-table design:** put multiple entity types in **one table**, using key prefixes
  (`USER#1`, `ORDER#9`) so related items share a partition and come back in one query. Advanced but
  idiomatic.
- **GSI (Global Secondary Index):** a different PK/SK to support an **additional** access pattern;
  it's a replicated copy with its own keys.
- **LSI (Local Secondary Index):** alternate SK, same PK (must be created with the table).

---

## 5. Consistency, capacity & cost

- Reads are **eventually consistent** by default; you can request **strongly consistent** reads
  (more cost, same-region only).
- **On-demand** vs **provisioned** capacity (RCU/WCU); throttling if you exceed it.
- **Conditional writes** give optimistic concurrency (write only if a version matches) — Lesson 02.
- **Avoid `Scan`** (reads the whole table) — always design to `Query` by key.

---

## 🎤 Say this in the interview

- *"DynamoDB is access-pattern-first: I list every query, then design partition and sort keys so
  each is a single efficient Query — never a Scan."*
- *"The partition key must be high-cardinality to spread load; for an unavoidable hot key I
  write-shard it and fan out reads."*
- *"Reads are eventually consistent by default; I use GSIs for extra access patterns and conditional
  writes for optimistic concurrency."*

➡️ **Next:** [06 — Replication & sharding](./06_Replication_And_Sharding.md)
