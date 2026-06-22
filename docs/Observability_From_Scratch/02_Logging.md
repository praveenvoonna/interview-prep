# 02 — Logging

## 🧠 The One Idea

**A log is a timestamped event — "this happened" — and making logs *structured* (key/value, not free
text) turns them from a haystack into a searchable database.** When every log line carries the same
**correlation ID**, you can stitch one request's whole journey together in seconds.

The one-liner: **"Structured, leveled logs with correlation IDs make events queryable and let you
trace one request across services — the core of my MTTR-reduction story."**

---

## 1. Structured vs unstructured

- **Unstructured:** `"user 42 failed login from 1.2.3.4"` — human-readable, machine-hostile.
- **Structured (JSON):** queryable fields:

```json
{"ts":"...","level":"warn","msg":"login failed","user_id":42,"ip":"1.2.3.4","request_id":"abc"}
```

Now you can filter `level=warn AND user_id=42` instantly. **Structured logging is the single biggest
upgrade** to log usefulness.

---

## 2. Log levels (and using them right)

| Level | Use for |
|---|---|
| **ERROR** | something failed and needs attention |
| **WARN** | recoverable/abnormal but handled |
| **INFO** | key business events (request handled, job done) |
| **DEBUG** | detailed diagnostics (off in prod normally) |
| **TRACE** | very fine-grained |

Set the level by environment; too much INFO/DEBUG in prod is noise and cost.

---

## 3. Correlation IDs (the MTTR superpower)

- Assign a **request ID / trace ID** at the edge and attach it to **every** log line for that
  request (propagate it downstream via headers/metadata).
- Now one filter shows the request's full path across services → no more guessing which logs belong
  together.
- This is exactly the "structured logging cut MTTR during escalations" story — make it concrete.

---

## 4. What (and what not) to log

- **Do:** errors with context, key state transitions, decisions, IDs, timings.
- **Don't:** secrets, PII, tokens, full payloads (security + cost). Redact.
- **Add context, not noise:** a log without the IDs/fields to act on is just clutter.
- Be mindful of **cost** — logs are voluminous; sample high-volume debug logs.

---

## 5. The logging pipeline

```
app (JSON logs) → collector (Fluent Bit/Vector) → store/search (Loki/Elasticsearch)
                                                   → query/visualize (Grafana/Kibana)
```

- **Centralized logging** aggregates all services in one searchable place.
- **Loki** (label-indexed, Prometheus-style) or the **ELK/OpenSearch** stack are common.
- Tie logs to traces via the shared **trace ID** (Lesson 03).

---

## 🎤 Say this in the interview

- *"I log structured JSON with levels, so logs are queryable fields, not free text — `level=error AND
  service=x` instead of grep."*
- *"I attach a correlation/trace ID at the edge and propagate it, so one filter reconstructs a
  request's whole path across services — that's what cut our MTTR during escalations."*
- *"I never log secrets or PII, add context not noise, and ship logs to a central store like Loki or
  Elasticsearch correlated with traces."*

➡️ **Next:** [03 — Tracing](./03_Tracing.md)
