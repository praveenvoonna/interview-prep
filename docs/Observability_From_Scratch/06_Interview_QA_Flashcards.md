# 06 — Interview Q&A Flashcards

Rapid-fire. Cover the answer, say it aloud, then check. Read twice.

---

## Foundations

**Q: Monitoring vs observability?**
Monitoring = known-unknowns via predefined dashboards/alerts; observability = ask new questions of a
live system to debug unknown-unknowns.

**Q: The three pillars and what each answers?**
Metrics = *what*, traces = *where*, logs = *why*.

**Q: What's the business value?**
Lower MTTR — faster detection and resolution, less downtime.

**Q: What is OpenTelemetry?**
A vendor-neutral standard to generate/export metrics, logs, and traces — instrument once, send
anywhere.

---

## Metrics & Prometheus

**Q: Metric types?**
Counter (up only), gauge (up/down), histogram (latency buckets), summary (client quantiles).

**Q: Push or pull?**
Pull — Prometheus scrapes `/metrics`; it can detect a target being down.

**Q: The cardinality trap?**
Each label combo is a series; high-cardinality labels (user/request IDs) explode memory and can
crash Prometheus.

**Q: PromQL for request rate and p99?**
`rate(metric[5m])`; `histogram_quantile(0.99, sum by (le)(rate(bucket[5m])))`.

---

## Logging

**Q: Structured vs unstructured?**
Structured = queryable key/value (JSON); unstructured = free text you have to grep.

**Q: The MTTR superpower?**
Correlation/trace IDs on every log line → reconstruct one request's whole path.

**Q: What not to log?**
Secrets, PII, tokens, full payloads — redact.

---

## Tracing

**Q: Trace vs span?**
Span = one timed unit of work; trace = the tree of spans for one request.

**Q: How do hops link across services?**
A trace ID propagated in call headers (e.g. W3C traceparent) creates child spans across processes.

**Q: Why sample, and what to keep?**
Cost at scale; always keep error and high-latency traces (tail-based is smarter).

---

## Alerting & SLOs

**Q: SLI vs SLO vs SLA?**
SLI = measured number; SLO = target for it; SLA = contract with consequences.

**Q: What's an error budget?**
The allowed failure (1 − SLO); spend it on velocity, freeze when it's burning.

**Q: Alert on what?**
Burn rate against the budget and user-facing symptoms — not every CPU spike (avoid alert fatigue).

**Q: RED vs USE?**
RED = Rate/Errors/Duration (services); USE = Utilization/Saturation/Errors (resources).

---

## Debugging

**Q: The incident loop?**
metric → trace → log → fix → verify, threaded by a correlation ID.

**Q: Dashboard top priority?**
Golden signals / RED + SLO burn — health in 5 seconds.

---

🎉 **You finished Observability From Scratch.** Lead with **three pillars (metrics/logs/traces) →
lower MTTR**, then walk the loop **metric → trace → log → fix**, and close on **SLOs +
error budgets**. That arc answers any "how do you run things in production?" question.
