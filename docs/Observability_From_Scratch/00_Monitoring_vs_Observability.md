# 00 — Monitoring vs Observability

## 🧠 The One Idea

**Monitoring tells you *that* something is wrong (a dashboard light turns red); observability lets you
ask *why* — even for problems you never anticipated.** Monitoring watches known failure modes;
observability gives you enough signal to investigate brand-new ones without shipping more code.

The one-liner: **"Monitoring answers known-unknowns with predefined dashboards; observability answers
unknown-unknowns by letting you ask new questions of a running system."**

---

## 1. The distinction

- **Monitoring:** predefined checks/alerts for **things you already expect** ("CPU > 80%", "5xx
  rate"). Known-unknowns.
- **Observability:** the *property* of a system that you can **understand its internal state from its
  outputs** — debug novel issues you didn't predict. Unknown-unknowns.

Monitoring is a subset of what good observability enables.

---

## 2. The three pillars

| Pillar | Answers | Example |
|---|---|---|
| **Metrics** | *what* is wrong (aggregate numbers) | latency p99 spiked |
| **Logs** | *why* (discrete events) | "DB timeout on query X" |
| **Traces** | *where* (request path across services) | the call stalled in service B |

Together: metrics alert you, traces localize it, logs explain it.

---

## 3. Why it matters

- In production, **Prometheus metrics + structured logging cut MTTR** during incidents — that's
  observability turning a multi-hour hunt into a minutes-long lookup.
- The business value is **MTTR** (mean time to resolution): good telemetry directly shrinks downtime
  and toil.

---

## 4. MTTR and the incident lifecycle

```
detect → triage → localize → diagnose → fix → verify
  ↑metrics  ↑metrics  ↑traces   ↑logs    ...   ↑metrics
```

Each pillar accelerates a different stage; missing one leaves a blind spot that stretches MTTR.

---

## 5. Telemetry & standards

- **Telemetry** = the data your system emits (metrics/logs/traces).
- **OpenTelemetry (OTel):** the vendor-neutral standard for generating and exporting all three —
  instrument once, send anywhere (Prometheus, Jaeger, Grafana, Datadog).
- Design for observability **up front** (structured logs, useful metrics, trace context) — it's hard
  to bolt on later.

---

## 🎤 Say this in the interview

- *"Monitoring answers known-unknowns with predefined dashboards; observability lets me ask new
  questions of a live system to debug problems I never anticipated."*
- *"It rests on three pillars — metrics say *what*, traces say *where*, logs say *why* — and together
  they shrink MTTR."*
- *"Prometheus metrics plus structured logging cut resolution time during escalations;
  I instrument with OpenTelemetry so I'm not locked to one backend."*

➡️ **Next:** [01 — Metrics & Prometheus](./01_Metrics_And_Prometheus.md)
