# 05 — Consistency & CAP

## 🧠 The One Idea

**When the network between your servers breaks, you must choose: keep answering with maybe-stale
data, or refuse to answer until you're sure it's correct.** That forced choice — during a network
partition — between **availability** and **consistency** is the CAP theorem. You can't have both
*while partitioned*; you pick which one matters for this system.

The one-liner: **"During a network partition you choose Consistency or Availability — CAP is about
that trade-off, not everyday operation."**

---

## 1. CAP, precisely

- **C**onsistency — every read sees the latest write.
- **A**vailability — every request gets a (non-error) response.
- **P**artition tolerance — the system keeps working despite dropped messages between nodes.

Partitions **will** happen in a distributed system, so P is non-negotiable → the real choice is
**CP vs AP**.

- **CP** (e.g. etcd, ZooKeeper, traditional RDBMS): refuse/stale-block to stay correct.
- **AP** (e.g. Dynamo-style, Cassandra): keep serving, reconcile later (eventual consistency).

---

## 2. The consistency spectrum

It's not binary:

- **Strong consistency:** reads always see the latest write (single-leader, quorum reads).
- **Eventual consistency:** replicas converge "eventually"; reads may be stale briefly.
- **Read-your-writes / monotonic reads:** useful middle grounds for UX.
- **Causal consistency:** preserves cause→effect ordering.

Match the level to the use case: bank balance = strong; like-count = eventual.

---

## 3. Quorums (how distributed DBs tune it)

With N replicas, require **W** acks on write and **R** on read:

- **W + R > N → strong-ish consistency** (read and write sets overlap).
- Smaller W/R → faster, more available, weaker consistency.
- This is the **tunable consistency** of Cassandra/Dynamo — you dial the CAP trade-off per query.

---

## 4. PACELC (the grown-up CAP)

CAP only covers partitions. **PACELC:** *if Partition → choose A or C; Else (normal operation) →
choose Latency or Consistency.* Even without partitions, **strong consistency costs latency**
(coordination). Mentioning PACELC signals depth.

---

## 5. Applying it

- **Money/inventory/auth:** choose **consistency** (CP) — a wrong answer is unacceptable.
- **Feeds/counts/recommendations:** choose **availability** (AP) — stale is fine, downtime isn't.
- **Control planes** (your world): config/leader state uses **CP stores like etcd** so all nodes
  agree (Lesson 06).

---

## 🎤 Say this in the interview

- *"Partitions are inevitable, so CAP really means CP vs AP: under a partition do I stay correct
  or stay available? I choose per use case."*
- *"Consistency is a spectrum — strong vs eventual — and with quorums, W+R>N gives strong-ish
  reads; I'd dial it per query."*
- *"PACELC adds that even without partitions, strong consistency costs latency — so for a feed I'd
  go eventual, for balances I'd go strong."*

➡️ **Next:** [06 — Consensus](./06_Consensus.md)
