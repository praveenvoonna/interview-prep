# 01 — Architecture Big Picture

## 🧠 The One Idea

**A Kafka cluster is a team of identical servers (brokers) that split the work of storing and
serving your logs.** No single broker holds everything; each holds a slice (some partitions).
One broker is elected the **controller** — the team lead that hands out assignments and
reacts when a teammate dies. You talk to *any* broker and it routes you to the right one.

The common one-liner: **"Kafka is a cluster of brokers coordinated by a controller."**

---

## 1. The pieces

- **Broker** — one Kafka server process. It stores partition data on disk and serves
  produce/fetch requests. A broker has an **id** and listens on a host:port.
- **Cluster** — multiple brokers. Partitions and their replicas are spread across them so the
  cluster survives a broker dying.
- **Controller** — one broker plays this extra role: it manages partition **leadership**,
  tracks which brokers are alive, and drives **leader election** on failure.
- **Client** — producers and consumers. They use any broker as a **bootstrap** server, then
  learn the full cluster layout (metadata) and connect directly to partition leaders.

---

## 2. How a client finds its data

1. Client connects to a **bootstrap.servers** broker (any one or a few).
2. It asks for **metadata**: "for topic `orders`, which broker leads partition 0, 1, 2…?"
3. The broker replies with the map. The client then talks **directly to the leader** of each
   partition it needs. No proxy hop in the hot path — that's part of why Kafka is fast.

So the bootstrap broker is just an entry point; it is **not** a bottleneck.

---

## 3. ZooKeeper vs KRaft — who stores cluster metadata?

Kafka needs somewhere to keep *cluster* state (brokers alive, topics, partition leaders, configs).

- **Old way — ZooKeeper:** a separate system Kafka depended on for metadata and controller
  election. Extra moving part to run, scale, and secure.
- **New way — KRaft (Kafka Raft):** Kafka stores its **own** metadata in an internal Kafka log,
  managed by a quorum of **controller** nodes using the **Raft** consensus protocol. No
  ZooKeeper. Simpler ops, faster failover, scales to far more partitions.

**Say the timeline:** ZooKeeper was the original; **KRaft is the default/standard now** and
ZooKeeper mode is being removed. New clusters should use KRaft.

---

## 4. What lives on a broker's disk

Each partition is a directory of **segment files** — the actual append-only log on disk. Kafka
writes **sequentially** (append to the end), which is why spinning disks and SSDs both fly:
sequential I/O is far faster than random I/O. We cover segments in Lesson 06.

---

## 5. The reconcile-on-failure flow (one picture)

```
Broker 3 dies (it led orders-2)
        │
        ▼
Controller notices (heartbeat/metadata) ──► picks a surviving in-sync replica
        │                                    as the NEW leader for orders-2
        ▼
Controller broadcasts new metadata ──► clients refresh, talk to the new leader
```

The cluster keeps serving with a brief blip for the affected partitions only.

---

## 🎤 Say this in the interview

- *"A Kafka cluster is brokers that each store a subset of partitions; one broker acts as the
  **controller** managing leadership and failover."*
- *"Clients bootstrap to any broker, fetch metadata, then talk **directly to partition
  leaders** — no proxy in the data path."*
- *"Metadata used to live in **ZooKeeper**; modern Kafka uses **KRaft** (Raft-based controllers)
  with no ZooKeeper — simpler and scales to more partitions."*

➡️ **Next:** [02 — Topics, partitions & offsets](./02_Topics_Partitions_Offsets.md)
