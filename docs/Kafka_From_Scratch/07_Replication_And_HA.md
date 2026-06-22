# 07 — Replication, ISR & High Availability

## 🧠 The One Idea

**Every partition is copied onto several brokers so one machine dying doesn't lose your data.**
One copy is the **leader** (handles all reads/writes); the others are **followers** that copy
the leader. The followers that are caught up form the **in-sync replicas (ISR)**. If the leader
dies, one of the in-sync followers is promoted — no data loss, brief blip.

The common one-liner: **"Each partition has a leader and follower replicas; the ISR is the set
that's caught up and eligible to take over."**

---

## 1. Replication basics

- **Replication factor (RF)** — how many copies of each partition exist (typically **3**).
- For each partition: **1 leader + (RF−1) followers**, spread across different brokers.
- **All produces and consumes go to the leader.** Followers just **replicate** by fetching from
  the leader (like a consumer). (Newer Kafka allows follower-fetch for locality, but assume
  leader-based for interviews.)

---

## 2. ISR — In-Sync Replicas

- The **ISR** is the set of replicas (including the leader) that are **fully caught up** with the
  leader within `replica.lag.time.max.ms`.
- A follower that falls behind (slow/dead) is **dropped from the ISR**; it rejoins when it
  catches up.
- **Why it matters:** with `acks=all`, the leader waits for **all ISR members** to have the
  record before acking. Only ISR members are eligible to become leader (by default).

---

## 3. The durability contract (the senior answer)

Three settings work **together**:

- **`replication.factor=3`** — three copies exist.
- **`acks=all`** — producer waits for all in-sync replicas.
- **`min.insync.replicas=2`** — if fewer than 2 replicas are in-sync, **reject the write**
  (producer gets an error) instead of accepting data that isn't safely replicated.

With RF=3 + `min.insync.replicas=2` + `acks=all`, you can **lose one broker** and still accept
writes with **zero data loss**. Lose two and writes stop (safe) but committed data survives.

---

## 4. Leader election & failover

- The **controller** (Lesson 01) detects a dead broker and **elects a new leader** from the ISR
  for each affected partition, then propagates new metadata. Clients refresh and continue.
- **Unclean leader election** (`unclean.leader.election.enable`): if **no** ISR replica is
  available, do you promote an **out-of-sync** replica?
  - `false` (default, recommended) → wait for an ISR replica; **prioritize consistency** (no
    data loss) over availability.
  - `true` → promote a stale replica; **prioritize availability** but **risk data loss**.
  This is Kafka's CAP-style knob — be ready to discuss the trade-off.

---

## 5. Spreading risk

- **Rack awareness** (`broker.rack`) places replicas across racks/AZs so one rack/AZ failure
  doesn't take out all replicas of a partition.
- For multi-region/DR, replicate **between clusters** with **MirrorMaker 2** (async; covered in
  Lesson 08), not by stretching one cluster across far regions.

---

## 🎤 Say this in the interview

- *"Each partition has a leader and follower replicas; the **ISR** is the caught-up set. All I/O
  goes through the leader; followers fetch to stay in sync."*
- *"Durability = **RF=3 + acks=all + min.insync.replicas=2**: survive one broker loss with no
  data loss; under-replicated writes are rejected rather than silently accepted."*
- *"**Unclean leader election** is the consistency-vs-availability knob — I keep it **off** to
  avoid data loss; **rack awareness** spreads replicas across AZs."*

➡️ **Next:** [08 — The Kafka ecosystem](./08_Ecosystem.md)
