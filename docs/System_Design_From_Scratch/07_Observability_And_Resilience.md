# 07 — Observability & Resilience

## 🧠 The One Idea

**You can't fix what you can't see, and you can't survive failures you didn't plan for.**
Observability is the dashboard and warning lights of your system; resilience is the seatbelts and
crumple zones. Together they're how a senior engineer keeps a distributed system healthy — and
a frequent source of strong, concrete interview stories (e.g. cutting MTTR with structured
logging during incidents).

The one-liner: **"Observability (metrics/logs/traces) tells you what's wrong; resilience patterns
(timeouts/retries/circuit breakers) stop one failure becoming an outage."**

---

## 1. The three pillars (preview of the Observability course)

- **Metrics** — numeric time series (QPS, latency, error rate). *What* is wrong.
- **Logs** — discrete events; **structured** logs with correlation IDs cut debugging time. *Why*.
- **Traces** — a request's path across services. *Where*.

Together they shrink **MTTR** — the metric that actually matters in production.

---

## 2. SLI / SLO / error budgets

- **SLI** — a measured indicator (e.g. p99 latency, success rate).
- **SLO** — the target (e.g. 99.9% of requests < 200ms).
- **Error budget** — the allowed failure (0.1%); spend it on releases, freeze when exhausted.

This vocabulary signals you think about reliability as an engineering quantity, not a vibe.

---

## 3. Resilience patterns (the must-knows)

- **Timeouts:** never wait forever — every network call has a deadline (Go `context`).
- **Retries with backoff + jitter:** for transient failures; jitter avoids synchronized retry
  storms. (Your route-add retry framework is exactly this.)
- **Circuit breaker:** after N failures, "open" and fail fast instead of hammering a sick
  dependency; "half-open" to test recovery.
- **Bulkheads:** isolate resources so one overloaded component can't sink the whole app.
- **Graceful degradation:** serve a reduced experience (cached/partial) rather than erroring out.

---

## 4. Avoiding cascading failures

One slow dependency can exhaust threads/connections everywhere upstream — a **cascade**. Defences:

- Timeouts + circuit breakers stop the pile-up.
- **Load shedding** — reject excess load early to protect the core.
- **Backpressure** — propagate "slow down" upstream (Lesson 04).
- **Idempotency** — so retries are safe.

---

## 5. Health checks & rollouts

- **Liveness vs readiness** (Kubernetes): liveness restarts a stuck pod; readiness removes it from
  the LB until it can serve.
- **Rolling / canary / blue-green** deploys limit blast radius; **rollback** fast on regression.
- Pair with the error budget: stop rollouts when reliability dips.

---

## 🎤 Say this in the interview

- *"I instrument with metrics, structured logs and traces to cut MTTR — structured
  logging with context turns multi-hour escalations into minutes."*
- *"For resilience I add timeouts, retries with backoff and jitter, and circuit breakers so one
  sick dependency fails fast instead of cascading."*
- *"I define SLOs with an error budget and use readiness probes plus canary rollouts to limit
  blast radius."*

➡️ **Next:** [08 — Worked examples](./08_Worked_Examples.md)
