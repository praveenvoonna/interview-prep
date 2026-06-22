# 📈 Observability From Scratch — A Crash Course (in plain English)

A from-zero course on **metrics, logs and traces** — turning real production practice (Prometheus
metrics, structured logging that cuts MTTR during incidents) into a confident, structured
answer for any "how do you debug production?" question.

> **How to read this:** go in order, 00 → 06. Each lesson starts with a plain-English analogy
> (**"The One Idea"**), then the real mechanics, then **"Say this in the interview"** lines.

> **Status:** 📝 *Syllabus ready — lessons to be written.* This README is the outline and the
> reference you can study from today.

---

## 📂 The lessons (planned, read in order)

### Part A — The idea
- **00 — Monitoring vs observability** — known-unknowns vs unknown-unknowns; the three pillars.

### Part B — The three pillars
- **01 — Metrics & Prometheus** — counters/gauges/histograms, scraping, PromQL, cardinality.
- **02 — Logging** — structured vs unstructured, levels, correlation IDs (your MTTR story).
- **03 — Tracing** — spans, context propagation, distributed traces across services.

### Part C — Acting on it
- **04 — Alerting & SLOs** — SLI/SLO/SLA, error budgets, alert fatigue, the RED/USE methods.
- **05 — Dashboards & debugging a real incident** — from alert → metric → log → trace → fix.

### Part D — Recall
- **06 — Interview Q&A flashcards** — pillars/PromQL/SLO rapid-fire.

---

## 🧠 The whole course in three sentences
1. **Observability is being able to ask new questions of a running system** without shipping new
   code — built on metrics, logs and traces.
2. **Each pillar answers a different question:** metrics say *what* is wrong, traces say *where*,
   logs say *why*.
3. **Good telemetry shrinks MTTR:** structured logs with correlation IDs and the right metrics
   turn a multi-hour escalation into a minutes-long lookup.

---

## 🔗 Companion material
- **[Kubernetes From Scratch](../Kubernetes_From_Scratch/)** — where the Prometheus stack runs.
- **[System Design From Scratch](../System_Design_From_Scratch/)** — observability as a design pillar.

When the lessons are written, start at **00**.
