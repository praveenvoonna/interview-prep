# 08 — The Kafka Ecosystem

## 🧠 The One Idea

**Raw Kafka is just the log; the ecosystem is the set of power tools so you don't write
plumbing.** Need to pull data **in/out** of external systems without code? **Connect.** Need to
**transform** streams (joins, aggregations) with state? **Streams.** Need everyone to agree on
the message **shape**? **Schema Registry.** Need **SQL** over streams? **ksqlDB.**

The common one-liner: **"Connect moves data, Streams processes it, Schema Registry governs it."**

---

## 1. Kafka Connect — integration without code

- A framework for **streaming data between Kafka and external systems** using reusable,
  config-driven **connectors** (no custom code).
- **Source connector** = external system → Kafka (e.g. a DB via **CDC/Debezium**, S3, JDBC).
- **Sink connector** = Kafka → external system (e.g. Elasticsearch, S3, a data warehouse).
- Runs as a **distributed cluster of workers** with automatic offset tracking, restarts, and
  scaling. **Single Message Transforms (SMTs)** tweak records inline (rename, mask, route).
- **Use it when:** "get this database/table/bucket into Kafka (or out)" — the answer is almost
  always Connect, not a hand-written app.

---

## 2. Kafka Streams — stream processing as a library

- A **Java library** (not a separate cluster) for transforming topics → topics:
  `map`, `filter`, `join`, windowed **aggregations**, etc.
- **Stateful** processing uses local **state stores** (RocksDB) backed by **compacted
  changelog topics** → fault-tolerant state that's rebuilt on restart.
- Models data two ways: **KStream** (a record stream — events) and **KTable** (a changelog — the
  latest value per key, i.e. a table). The **stream/table duality** is the key concept.
- Scales by running more instances of your app (same `application.id`) — they form a group and
  split partitions, just like consumers.

---

## 3. Schema Registry — agree on message shape

- Stores and versions **schemas** (Avro / Protobuf / JSON Schema) so producers and consumers
  share a contract; messages carry a small **schema id** instead of the full schema.
- Enforces **compatibility** (backward/forward/full) so a producer can't push a change that
  breaks existing consumers. Critical for evolving events safely across teams.

---

## 4. ksqlDB & other pieces

- **ksqlDB** — write **SQL** over Kafka streams/tables (`CREATE STREAM … SELECT …`) instead of
  Java; good for quick transforms and materialized views.
- **MirrorMaker 2** — replicate topics **across clusters** (DR, multi-region, migration); built
  on Connect.
- **Clients** — official Java client plus librdkafka-based clients for Go, Python, .NET, etc.

---

## 5. Picking the right tool (cheat sheet)

| Need | Reach for |
|---|---|
| Get a DB/file/bucket in or out of Kafka | **Kafka Connect** (+ Debezium for CDC) |
| Join/aggregate/enrich streams with state | **Kafka Streams** (or ksqlDB for SQL) |
| Stop teams breaking each other's events | **Schema Registry** |
| Copy topics to another cluster/region | **MirrorMaker 2** |

---

## 🎤 Say this in the interview

- *"**Connect** is config-driven integration — source/sink connectors move data in and out of
  Kafka, with **Debezium** for database CDC."*
- *"**Kafka Streams** is a library, not a cluster: stateful transforms with RocksDB state stores
  backed by compacted changelogs; **KStream vs KTable** is the stream/table duality."*
- *"**Schema Registry** versions Avro/Protobuf schemas and enforces compatibility so events can
  evolve without breaking consumers."*

➡️ **Next:** [09 — Operations, performance & gotchas](./09_Operations_And_Gotchas.md)
