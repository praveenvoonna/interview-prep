# 05 — Dashboards & Debugging a Real Incident

## 🧠 The One Idea

**The three pillars shine when you use them *in sequence* during an incident: metric → trace → log →
fix.** A good engineer doesn't stare at one dashboard — they follow the signal from "what's wrong"
down to "here's the exact line", fast. This lesson is that drill.

The one-liner: **"Alert fires (metric) → narrow to the slow/failing service (trace) → read the error
context (log) → fix → verify on the metric. That's the MTTR-shrinking loop."**

---

## 1. The debugging funnel

```
ALERT  →  METRICS        →  TRACES            →  LOGS              →  FIX  →  VERIFY
(symptom) (which signal,    (which service/call  (why — error,        (metric returns
          how bad, since)    owns the latency)    stacktrace, IDs)     to normal)
```

Each pillar hands off to the next. Skipping steps = guessing.

---

## 2. A worked incident

**Alert:** "checkout error rate > 2% (SLO burn)".

1. **Metrics:** error rate jumped at 14:03; latency p99 up too; isolated to `payment-service`.
2. **Traces:** checkout traces show `payment-service → bank-api` span timing out at ~3s.
3. **Logs:** filter by that trace ID → `connection pool exhausted` warnings in payment-service.
4. **Root cause:** a bank-api slowdown + small connection pool → exhaustion → cascading errors.
5. **Fix:** add timeouts/circuit breaker on bank-api, raise pool size, deploy.
6. **Verify:** error rate back under SLO; burn-rate alert clears.

The correlation **ID is the thread** linking metric → trace → log.

---

## 3. Dashboard design

- **Top:** the **golden signals / RED** (rate, errors, duration) + SLO burn — the at-a-glance health.
- **Middle:** per-dependency breakdowns (downstream latency, DB, queue depth).
- **Bottom:** resource **USE** (CPU, memory, saturation).
- One screen should answer "are we healthy, and if not, roughly where?" in **5 seconds**.

---

## 4. Habits that shrink MTTR

- **Correlation IDs everywhere** (so the funnel works).
- **Runbooks** linked from alerts (what to check, who to call).
- **Deploy markers** on dashboards (most incidents follow a deploy — correlate instantly).
- **Blameless postmortems** → feed fixes back into alerts/SLOs.

---

## 5. The narrative for interviews

Tell it as a story: *symptom → localize → diagnose → fix → verify*, naming which pillar you used at
each step and how telemetry turned a multi-hour escalation into minutes. That structure alone signals
seniority.

---

## 🎤 Say this in the interview

- *"My loop is metric → trace → log → fix → verify: the alert tells me *what*, the trace tells me
  *where*, the log tells me *why*, and I confirm on the metric."*
- *"That funnel — anchored by a correlation ID across all three — turns multi-hour
  escalations into minutes; that's the MTTR win."*
- *"I build dashboards golden-signals-first, link runbooks from alerts, and annotate deploys since most
  incidents follow a release."*

➡️ **Next:** [06 — Interview Q&A flashcards](./06_Interview_QA_Flashcards.md)
