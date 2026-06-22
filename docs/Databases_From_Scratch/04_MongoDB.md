# 04 — MongoDB

## 🧠 The One Idea

**MongoDB stores data the way your app already thinks about it: as documents (JSON), each holding
everything one entity needs.** Instead of splitting an order across five tables and joining them
back, you keep the order — with its items — in one document. Flexible schema, fast reads of whole
entities, at the cost of weaker cross-document guarantees.

The one-liner: **"MongoDB is a document store: model around access patterns, embed what you read
together, reference what you don't."**

---

## 1. The data model

- **Database → collections → documents** (BSON, binary JSON).
- Documents in a collection can have **different fields** (flexible schema).
- A document can **nest** sub-documents and arrays — no joins needed to read them.

```json
{ "_id": 1, "customer": "Ann",
  "items": [ {"sku": "A1", "qty": 2}, {"sku": "B7", "qty": 1} ] }
```

---

## 2. Embedding vs referencing (the key decision)

- **Embed** when data is read together and is bounded → one fast read (e.g. order + its items).
- **Reference** (store an id, look up separately) when data is large, shared, or grows unbounded
  (e.g. a user's millions of events).
- Rule of thumb: **"data that's accessed together is stored together"** — and avoid unbounded
  arrays inside a document (the 16MB doc limit).

---

## 3. Indexes in MongoDB

- Same B-tree idea as SQL: `db.coll.createIndex({ field: 1 })`.
- **Compound indexes** follow the **left-prefix rule** (like SQL, Lesson 01).
- **Multikey** indexes index array elements; **text** and **geospatial** indexes too.
- Use `explain()` to confirm an index is used (the Mongo equivalent of `EXPLAIN`).

---

## 4. The aggregation pipeline

Mongo's query power comes from a **pipeline** of stages (data flows stage→stage):

```js
db.orders.aggregate([
  { $match:  { status: "paid" } },        // filter (use an index here)
  { $group:  { _id: "$customer",
               total: { $sum: "$amount" } } }, // aggregate
  { $sort:   { total: -1 } },             // order
  { $limit:  10 }
])
```

Think of `$match/$group/$sort` as SQL's `WHERE/GROUP BY/ORDER BY`. Put `$match` **first** to filter
early (and hit an index).

---

## 5. Consistency & scale

- **Atomicity** is guaranteed at the **single-document** level; multi-document **transactions**
  exist (4.0+) but cost more — another reason to embed.
- **Replica sets:** primary + secondaries → HA and read scaling; reads can be tuned with **read
  preference** and **read/write concern** (Mongo's quorum dial).
- **Sharding:** horizontal scale by a **shard key** — choose it carefully to avoid hot shards
  (same trade-off as the System Design course).

---

## 🎤 Say this in the interview

- *"MongoDB is a document store — I embed data that's read together for single-read access, and
  reference data that's large or unbounded, mindful of the 16MB document limit."*
- *"Single-document writes are atomic; I lean on embedding so I rarely need multi-document
  transactions."*
- *"The aggregation pipeline is `$match`→`$group`→`$sort`; I filter first to use an index, and I
  pick a shard key that avoids hot shards."*

➡️ **Next:** [05 — DynamoDB data modeling](./05_DynamoDB_Data_Modeling.md)
