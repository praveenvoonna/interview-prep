# 03 — Tracing

## 🧠 The One Idea

**A distributed trace is the GPS breadcrumb trail of one request as it hops across services — it shows
exactly *where* the time went.** In a microservice system, a single user click might touch ten
services; tracing stitches those hops into one timeline so you can see which service is the
bottleneck.

The one-liner: **"A trace follows one request across services as a tree of timed spans, so I can see
where latency and errors actually occur."**

---

## 1. Traces and spans

- A **span** = one unit of work (a function, a DB call, an RPC) with a start/end time and metadata.
- A **trace** = all spans for one request, linked into a **tree** (parent → child).
- Each span records duration, service, operation, status, and **attributes** (tags).

```
[Trace: GET /checkout] ───────────────── 420ms
  ├─ api-gateway            10ms
  ├─ order-service          50ms
  │    └─ db query          35ms
  └─ payment-service       350ms  ← the bottleneck is obvious
```

---

## 2. Context propagation (how hops link)

- A **trace ID** is generated at the edge and **passed to every downstream call** (HTTP/gRPC
  headers, e.g. W3C `traceparent`).
- Each service creates child spans under the incoming context → the tree assembles across process
  boundaries.
- The **same trace ID also tags your logs** (Lesson 02) — unifying all three pillars.

---

## 3. Why tracing is essential in microservices

- Metrics say "checkout is slow"; tracing says **which of the ten services** and **which call** is
  slow.
- It reveals **dependencies** and **fan-out** you might not have known existed.
- It catches **tail latency** — the one slow downstream dragging p99.

This is the *where* that metrics and logs alone can't give you.

---

## 4. Sampling (the cost control)

- Tracing every request is expensive at scale → **sample**.
- **Head-based:** decide at the start (e.g. keep 1%). Simple, but may miss rare errors.
- **Tail-based:** decide after seeing the trace (keep all errors + slow ones). Smarter, more
  infrastructure.
- Always **keep error and high-latency traces** — those are the ones you need.

---

## 5. OpenTelemetry & backends

- **OpenTelemetry (OTel):** the standard SDK/agent to instrument and export traces (and metrics/
  logs) — vendor-neutral.
- **Backends:** Jaeger, Tempo, Zipkin, or commercial (Datadog, Honeycomb).
- **Auto-instrumentation** covers common frameworks (HTTP servers, DB clients) with little code;
  add manual spans around your own critical sections.

---

## 🎤 Say this in the interview

- *"A trace follows one request across services as a tree of timed spans, so I can see exactly which
  service and call owns the latency — the *where* metrics can't give me."*
- *"It works by propagating a trace ID through call headers so child spans link across processes, and
  I tag logs with the same ID to unify the pillars."*
- *"At scale I sample — keeping all error and slow traces — and instrument with OpenTelemetry into
  something like Jaeger or Tempo."*

➡️ **Next:** [04 — Alerting & SLOs](./04_Alerting_And_SLOs.md)
