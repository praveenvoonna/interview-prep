# 06 — Consensus

## 🧠 The One Idea

**Consensus is how a group of unreliable servers agree on one answer, even when some are slow or
dead — like a committee that elects a chairperson and only accepts decisions a majority signed
off.** It's how distributed systems pick a single leader and keep one consistent copy of critical
state (config, locks, membership) across machines.

The one-liner: **"Consensus (Raft/Paxos) lets a majority of nodes agree on a single, replicated
log of decisions despite failures."**

---

## 1. Why you need it

- Multiple nodes must agree on **one** value: who's the leader, what's the current config, who
  holds the lock.
- Naively letting each node decide → **split brain** (two leaders, divergent state).
- Consensus guarantees **safety** (never two conflicting decisions) and **liveness** (progress
  while a majority is up).

---

## 2. Raft in plain English

Raft is the understandable consensus algorithm (vs Paxos). Three pieces:

- **Leader election:** nodes are followers; if they hear no leader, a candidate requests votes.
  Win a **majority** → become leader for a **term**.
- **Log replication:** all writes go to the leader, which appends to its log and replicates to
  followers; an entry is **committed** once a **majority** has it.
- **Safety:** only entries on a majority can be committed, so a new leader always has them.

---

## 3. The majority (quorum) rule

- A cluster of **2f+1** nodes tolerates **f** failures (need a majority alive).
  - 3 nodes → survive 1 failure; 5 nodes → survive 2.
- **Why odd numbers:** 4 nodes still only tolerate 1 failure (majority of 4 is 3) — no benefit
  over 3, more cost. Always size clusters **odd**.

---

## 4. etcd — consensus you actually use

- **etcd** is a strongly-consistent key-value store built on Raft — the brain of Kubernetes
  (all cluster state) and many control planes.
- You've used etcd directly: **watches** for change notifications, and you added a **memory cache
  to throttle BFD watch load** — a real consensus-store interaction.
- It's a **CP** system (Lesson 05): under partition it stays consistent, refusing writes without a
  quorum.

---

## 5. Where consensus shows up in a design

- **Leader election** for a singleton job (only one scheduler active).
- **Distributed locks / leases** (etcd, ZooKeeper).
- **Config & service discovery** that all nodes must agree on.
- **Replicated state machines** — the general pattern: same log → same state everywhere.

Don't run consensus on the hot data path (it's coordination-heavy); use it for **control-plane
state**, and keep bulk data in scalable stores.

---

## 🎤 Say this in the interview

- *"For agreed-upon state like leader or config I'd use a Raft-based store like etcd: writes go to
  the leader and commit once a majority replicates them, which prevents split brain."*
- *"Size consensus clusters odd — 2f+1 tolerates f failures, so 3 survives 1, 5 survives 2; a
  4-node cluster buys nothing over 3."*
- *"etcd is CP: under a partition it refuses writes without quorum to stay consistent — that's why
  it backs Kubernetes' control plane."*

➡️ **Next:** [07 — Observability & resilience](./07_Observability_And_Resilience.md)
