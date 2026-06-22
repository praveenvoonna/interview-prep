# 01 — Scaling & Load Balancing

## 🧠 The One Idea

**Scaling up is hiring one super-employee; scaling out is hiring more ordinary employees.** A
bigger machine (vertical) is simple but hits a ceiling and is a single point of failure. More
machines (horizontal) scale almost without limit — *if* the work can be spread, which means making
your services **stateless** so any machine can handle any request. A **load balancer** is the
receptionist routing each request to a free worker.

The one-liner: **"Scale out with stateless services behind a load balancer; push state into shared
stores."**

---

## 1. Vertical vs horizontal

| | Vertical (scale up) | Horizontal (scale out) |
|---|---|---|
| How | bigger CPU/RAM | more machines |
| Limit | hardware ceiling | ~unlimited |
| Failure | single point | tolerant (N replicas) |
| Complexity | low | needs LB + statelessness |

Real systems scale **out** for resilience; you mention vertical as the quick early win.

---

## 2. Statelessness is the enabler

- A **stateless** service keeps no per-client memory between requests — any instance can serve any
  request, so you can add/remove instances freely.
- Push state to shared stores: **session in Redis**, **data in the DB**, **files in object
  storage**.
- This is exactly why you'd decouple state in a control plane — it's what makes horizontal scaling
  and rolling upgrades safe.

---

## 3. Load balancers (L4 vs L7)

- **L4 (transport):** routes by IP/port, very fast, protocol-agnostic (TCP/UDP).
- **L7 (application):** routes by HTTP path/host/headers — enables path-based routing, TLS
  termination, sticky sessions.
- **Algorithms:** round-robin, least-connections, IP-hash (sticky), weighted.
- **Health checks:** the LB stops sending traffic to unhealthy instances — the availability
  backbone.

---

## 4. Horizontal scaling patterns

- **Auto-scaling:** add instances when CPU/QPS crosses a threshold; remove when idle (ties to
  Kubernetes HPA / AWS ASG).
- **Stateless web tier + shared data tier** is the canonical 3-tier shape.
- **Read replicas** scale reads; **sharding** scales writes (Lesson 03).
- **CDN** offloads static content to the edge, near users.

---

## 5. Avoiding single points of failure

- Run **≥2 of everything** across **availability zones**.
- The LB itself needs redundancy (DNS failover / multiple LBs).
- Quantify with availability: 99.9% ≈ 8.7h downtime/yr; 99.99% ≈ 52min — each nine costs
  redundancy.

---

## 🎤 Say this in the interview

- *"I'll scale horizontally behind a load balancer and keep services stateless — session in Redis,
  data in the DB — so any instance can serve any request."*
- *"L4 balances on IP/port and is fast; L7 routes on HTTP and can terminate TLS and do path-based
  routing. Health checks drain unhealthy instances."*
- *"Read replicas scale reads, sharding scales writes; I'll run ≥2 of everything across AZs to
  avoid single points of failure."*

➡️ **Next:** [02 — Caching](./02_Caching.md)
