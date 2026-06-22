# 09 — Interview Q&A Flashcards

Rapid-fire. Cover the answer, say it aloud, then check. Read twice.

---

## Approach

**Q: First thing in a design interview?**
Clarify functional + non-functional requirements; pin scale, latency, consistency. Then estimate.

**Q: What are interviewers actually scoring?**
Your reasoning and trade-off articulation under ambiguity — not a single "correct" architecture.

---

## Scaling & LB

**Q: Vertical vs horizontal scaling?**
Up = bigger box (ceiling, SPOF); out = more boxes (needs statelessness + LB, near-unlimited).

**Q: What enables horizontal scaling?**
Stateless services — push state to Redis/DB/object storage so any instance serves any request.

**Q: L4 vs L7 load balancer?**
L4 routes by IP/port (fast); L7 by HTTP path/host (TLS termination, path routing). Health checks
drain bad instances.

---

## Caching

**Q: Default caching strategy?**
Cache-aside: check cache, on miss read DB and populate, with a TTL.

**Q: Hardest part of caching?**
Invalidation — combine TTLs with write-time invalidation/versioning.

**Q: Cache stampede fix?**
Jittered TTLs + request coalescing so one rebuild hits the DB.

---

## Data & sharding

**Q: Replication vs sharding?**
Replication scales reads + redundancy (full copies); sharding scales writes + storage (subsets by
key).

**Q: Hash vs range sharding?**
Hash = even spread, bad range queries; range = range queries, risks hot ranges.

**Q: What makes a good shard key?**
Even distribution + query locality; avoid resharding later.

---

## Async

**Q: Why a queue?**
Decouple producer/consumer, absorb spikes, retry on failure — at the cost of eventual consistency.

**Q: At-least-once implication?**
Possible duplicates → make consumers idempotent; persistent failures → DLQ.

**Q: Dual-write (DB + queue) fix?**
Outbox pattern: write row + message in one transaction, relay publishes.

---

## Consistency

**Q: CAP in one line?**
Under a partition choose Consistency or Availability (P is mandatory) → CP vs AP.

**Q: Quorum for strong reads?**
W + R > N.

**Q: What does PACELC add?**
Even without partitions, you trade Latency vs Consistency.

---

## Consensus

**Q: What does consensus give you?**
A majority agreeing on a single replicated log — leader election, config, locks; no split brain.

**Q: Cluster sizing?**
2f+1 tolerates f failures; use odd numbers (3 survives 1, 5 survives 2).

**Q: A consensus store you've used?**
etcd (Raft) — backs Kubernetes; e.g. using watches and caching to throttle BFD watch load. CP system.

---

## Observability & resilience

**Q: Three pillars?**
Metrics (what), logs (why), traces (where) — together cut MTTR.

**Q: Key resilience patterns?**
Timeouts, retries with backoff+jitter, circuit breakers, bulkheads, graceful degradation.

**Q: SLI/SLO/error budget?**
SLI measured, SLO target, error budget the allowed miss spent on releases.

**Q: Liveness vs readiness?**
Liveness restarts a stuck pod; readiness removes it from the LB until it can serve.

---

🎉 **You finished System Design From Scratch.** The winning habit: **clarify → estimate → design →
scale → fail**, narrating *"the trade-off is…"* at every step. That directly answers
"demonstrate stronger technical depth."
