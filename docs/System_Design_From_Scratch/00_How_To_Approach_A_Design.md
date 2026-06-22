# 00 — How to Approach a Design

## 🧠 The One Idea

**A design interview is a guided conversation, not a quiz with one right answer.** The interviewer
wants to see *how you think* under ambiguity: do you clarify before building, estimate scale,
justify trade-offs, and handle failure? Your job is to **drive a structured discussion**, not to
recall a perfect architecture.

The one-liner: **"Clarify → estimate → API → data → scale → failure: I narrate trade-offs at each
step."**

---

## 1. The 6-step framework

1. **Clarify requirements** — functional ("what must it do?") and non-functional (scale,
   latency, consistency, availability). Pin numbers.
2. **Estimate** — back-of-envelope: QPS, storage, bandwidth. Sets the scale of every later choice.
3. **API** — the handful of endpoints/RPCs that define the contract.
4. **Data model** — entities, access patterns, SQL vs NoSQL (Lessons 03, Databases course).
5. **High-level design** — boxes and arrows: clients → LB → services → data stores → queues.
6. **Scale & failure** — find bottlenecks, add caching/sharding/replication, discuss what breaks.

Spend the *most* time on 1 and 6 — that's where senior signal lives.

---

## 2. Requirements: ask before you build

Always nail down:

- **Who/what** uses it and the **core actions** (functional).
- **Scale:** users, reads vs writes, peak QPS, data size, growth.
- **Latency** target (p95/p99) and **availability** target.
- **Consistency** need: must reads be immediately correct, or is eventual OK?

Stating these explicitly is the #1 thing that separates senior from junior answers.

---

## 3. Back-of-envelope estimation

Quick numbers you should be able to produce:

- **QPS:** `daily requests / 86,400` (×2–3 for peak).
- **Storage:** `records/day × bytes/record × retention`.
- **Read:write ratio** — drives caching and replica decisions.

Round aggressively; the goal is the **order of magnitude** (thousands vs millions of QPS), which
decides whether one box suffices or you need sharding.

---

## 4. Narrate trade-offs (the senior signal)

There's no free lunch — name the cost of each choice:

- Cache → faster reads, but **staleness/invalidation**.
- SQL → strong consistency + joins, but **harder horizontal scale**.
- Async queue → decoupling + smoothing, but **eventual consistency + complexity**.

"I'd choose X because we prioritise Y over Z here" is the sentence that wins design rounds — and
directly answers the "show stronger technical depth" feedback you received.

---

## 5. Anti-patterns to avoid

- Jumping to a diagram before clarifying requirements.
- Inventing huge scale that wasn't asked for (over-engineering).
- Silent thinking — **think out loud**; the interviewer scores your reasoning.
- One-word answers ("use Kafka") with no justification.

---

## 🎤 Say this in the interview

- *"Before I design, let me clarify functional and non-functional requirements and pin down scale,
  latency and consistency targets."*
- *"Quick estimate: ~X QPS and ~Y storage, read-heavy — so I'll lean on caching and read
  replicas."*
- *"I'd pick X over Y because here we value [consistency/latency/availability] more than the cost
  it trades against."*

➡️ **Next:** [01 — Scaling & load balancing](./01_Scaling_And_Load_Balancing.md)
