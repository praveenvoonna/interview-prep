# 10 — Hands-On Lab

## 🧠 The One Idea

**You don't understand Kafka until you've watched a consumer group rebalance with your own
eyes.** This lab spins up a single broker, creates a partitioned topic, and lets you *see*
partitions, offsets, groups, and lag for real. Twenty minutes of doing beats hours of reading.

> **Goal:** run a broker → create a topic → produce → consume → add a second consumer and watch
> partitions get split → check lag.

---

## 1. Run a single-broker Kafka (KRaft, no ZooKeeper)

Easiest is Docker:

```bash
docker run -d --name kafka -p 9092:9092 apache/kafka:latest
# exec into it; the CLI scripts live under /opt/kafka/bin
docker exec -it kafka bash
cd /opt/kafka/bin
```

(All commands below run **inside** the container. `--bootstrap-server localhost:9092`.)

---

## 2. Create a topic with 3 partitions

```bash
./kafka-topics.sh --bootstrap-server localhost:9092 \
  --create --topic orders --partitions 3 --replication-factor 1

./kafka-topics.sh --bootstrap-server localhost:9092 --describe --topic orders
```

Read the `--describe` output: each partition lists its **Leader**, **Replicas**, and **Isr**.
With one broker all leaders are broker 1 and RF=1 (no HA — fine for a lab).

---

## 3. Produce some records (with keys)

```bash
./kafka-console-producer.sh --bootstrap-server localhost:9092 \
  --topic orders --property parse.key=true --property key.separator=:
# then type:
alice:placed order 1
alice:placed order 2
bob:placed order 1
```

Same key (`alice`) always lands in the **same partition** → those stay ordered (Lesson 02).

---

## 4. Consume from the beginning

```bash
./kafka-console-consumer.sh --bootstrap-server localhost:9092 \
  --topic orders --from-beginning \
  --property print.key=true --property print.partition=true
```

Note the data is **still there** after reading — Kafka retained it. Run this again and you
re-read everything: **replay**.

---

## 5. See a consumer group split partitions

Open **two** terminals, both joining group `g1`:

```bash
# terminal A and terminal B (same command in both):
./kafka-console-consumer.sh --bootstrap-server localhost:9092 \
  --topic orders --group g1 --property print.partition=true
```

Produce more records. With 3 partitions and 2 consumers, the group assigns partitions across
them — each consumer prints only its partitions. Start a **third** consumer → 1 partition each.
Start a **fourth** → it sits **idle** (more consumers than partitions, Lesson 04).

---

## 6. Inspect groups & lag

```bash
./kafka-consumer-groups.sh --bootstrap-server localhost:9092 --list
./kafka-consumer-groups.sh --bootstrap-server localhost:9092 \
  --describe --group g1
```

Read the columns: `PARTITION`, `CURRENT-OFFSET`, `LOG-END-OFFSET`, **`LAG`** (= end − current),
and which `CONSUMER-ID` owns each partition. Kill a consumer and re-run — watch the group
**rebalance** ownership to the survivors.

---

## 7. Try compaction (optional)

```bash
./kafka-topics.sh --bootstrap-server localhost:9092 --create --topic state \
  --partitions 1 --replication-factor 1 \
  --config cleanup.policy=compact
```

Produce several records with the **same key** and different values; after compaction kicks in,
only the latest value per key survives (Lesson 06).

---

## ✅ What you should now be able to say

- "I created a 3-partition topic, produced keyed records, and saw same-key records stay in one
  partition."
- "I added consumers to a group and watched partitions get reassigned — and a 4th consumer go
  idle."
- "I read **lag** from `kafka-consumer-groups --describe` and watched a rebalance on failure."

➡️ **Next:** [11 — Interview Q&A flashcards](./11_Interview_QA_Flashcards.md)
