# 01 — Metrics & Prometheus

## 🧠 The One Idea

**Metrics are cheap numbers over time — "how many", "how long", "how full" — and Prometheus scrapes
them from your services on a schedule and lets you query the trends.** They're the first line of
defense: small, aggregatable, and perfect for alerting and dashboards.

The one-liner: **"Prometheus pulls time-series metrics from `/metrics` endpoints; counters, gauges,
and histograms cover almost everything, and PromQL queries them."**

---

## 1. The four metric types

| Type | Meaning | Example |
|---|---|---|
| **Counter** | only goes up (reset on restart) | total requests, errors |
| **Gauge** | up and down | in-flight requests, memory, queue depth |
| **Histogram** | buckets of observations | request latency distribution |
| **Summary** | client-side quantiles | similar, less aggregatable |

**Counter for totals/rates, gauge for levels, histogram for latency** — that picks itself most of
the time.

---

## 2. Prometheus is pull-based

- Your service exposes a **`/metrics`** HTTP endpoint (via a client library).
- Prometheus **scrapes** it on an interval (e.g. every 15s) and stores the samples as time series.
- **Service discovery** (e.g. Kubernetes) tells Prometheus what to scrape.
- Pull means Prometheus controls load and can detect a target being **down** (scrape fails).

---

## 3. Labels & cardinality (the big gotcha)

- A metric + **labels** = a time series: `http_requests_total{method="GET",status="200"}`.
- Labels let you slice by dimension — but **every label combination is a separate series**.
- **High-cardinality labels** (user ID, request ID, email) **explode** memory and can take
  Prometheus down. Keep labels **bounded** (method, status, route — not unbounded IDs).

This is the #1 Prometheus operational mistake — name it.

---

## 4. PromQL essentials

```promql
rate(http_requests_total[5m])                      # per-second request rate
sum by (status) (rate(http_requests_total[5m]))    # grouped by status
histogram_quantile(0.99,
  sum by (le) (rate(http_latency_bucket[5m])))     # p99 latency
```

- **`rate()`** over a counter = per-second average — the workhorse.
- **`histogram_quantile`** turns histogram buckets into p50/p95/p99.

---

## 5. The ecosystem

- **Exporters:** turn third-party systems' stats into Prometheus metrics (node_exporter for hosts,
  DB/queue exporters).
- **Grafana:** dashboards on top of PromQL.
- **Alertmanager:** routes/dedupes alerts fired by Prometheus rules (Lesson 04).
- **Pushgateway:** for short-lived batch jobs that can't be scraped.

---

## 🎤 Say this in the interview

- *"Prometheus is pull-based: services expose `/metrics`, it scrapes on an interval and stores time
  series; I use counters for totals, gauges for levels, histograms for latency."*
- *"I keep label cardinality bounded — never user or request IDs as labels — because each combination
  is a series and high cardinality can take Prometheus down."*
- *"In PromQL, `rate()` over counters and `histogram_quantile` for p99 are my go-tos, visualized in
  Grafana and alerted via Alertmanager."*

➡️ **Next:** [02 — Logging](./02_Logging.md)
