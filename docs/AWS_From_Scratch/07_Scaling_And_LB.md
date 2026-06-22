# 07 — Scaling & Load Balancing

## 🧠 The One Idea

**Elasticity = a traffic cop spreading requests across many servers (load balancer) plus a
manager hiring and firing servers as demand changes (Auto Scaling).** Together they give you "add
capacity automatically when busy, remove it when quiet, and never send users to a dead server."
Decoupling with queues/topics makes the whole thing resilient to spikes.

The common one-liner: **"ELB distributes traffic; Auto Scaling adjusts capacity; queues decouple
producers from consumers."**

---

## 1. Elastic Load Balancing (ELB)

A managed load balancer that spreads incoming traffic across healthy targets in **multiple AZs**,
and does **health checks** so dead instances get no traffic.

- **ALB (Application LB)** — **Layer 7 (HTTP/HTTPS)**: path/host **routing**, WebSockets,
  containers, TLS termination. The default for web apps/microservices.
- **NLB (Network LB)** — **Layer 4 (TCP/UDP)**: ultra-high performance, static IPs, very low
  latency. For non-HTTP or extreme throughput.
- **GWLB** — for inserting network appliances (firewalls). (Niche — just name it.)

The LB sits in **public subnets**; targets live in **private subnets** (Lesson 03).

---

## 2. Auto Scaling

- An **Auto Scaling Group (ASG)** keeps a fleet between **min / desired / max** instances using a
  **launch template**.
- **Self-healing:** if an instance fails its health check, the ASG **replaces** it.
- **Scaling policies:**
  - **Target tracking** — "keep average CPU at 50%" (easiest, most common).
  - **Step / simple** — add/remove N on a CloudWatch alarm threshold.
  - **Scheduled** — scale ahead of known peaks (e.g. business hours).
- ASG + ELB together = traffic only goes to healthy instances, and capacity tracks demand.

**Scale out/in (horizontal, add/remove instances)** is the cloud-native default; **scale up/down
(vertical, bigger instance)** has limits and downtime — prefer horizontal.

---

## 3. Decoupling — SQS & SNS (resilience patterns)

- **SQS (queue)** — a buffer between producers and consumers. Producer drops messages in;
  workers pull at their own pace. Absorbs spikes, smooths load, enables retries/**DLQs**. *Async
  point-to-point.*
- **SNS (topic)** — **pub/sub** fan-out: one message → many subscribers (Lambdas, queues,
  emails). *One-to-many notifications.*
- **Fan-out pattern:** SNS topic → multiple SQS queues, each feeding a different service.
- **EventBridge** — event bus for event-driven architectures with routing rules (richer than
  SNS for app/SaaS events).

Decoupling means a slow/broken consumer doesn't take the producer down — work waits in the queue.

---

## 4. Edge & content scaling

- **CloudFront (CDN)** — cache content at edge locations near users; offloads origin and cuts
  latency.
- **Route 53** — DNS with health checks and routing policies (latency-based, weighted, failover)
  for global traffic distribution and DR.

---

## 5. Putting it together (the reference picture)

```
Route 53 → CloudFront → ALB (public subnets, multi-AZ)
                          │
                 Auto Scaling Group (app in private subnets, multi-AZ)
                          │
            SQS/SNS for async work → workers; RDS/DynamoDB for data
```

---

## 🎤 Say this in the interview

- *"**ALB** (L7, routing) or **NLB** (L4, performance) spread traffic across healthy targets in
  multiple AZs; **Auto Scaling** keeps capacity between min/desired/max and replaces unhealthy
  instances."*
- *"I prefer **target-tracking** scaling and **horizontal** scale-out over vertical."*
- *"I **decouple** with **SQS** (queue, absorb spikes, DLQs) and **SNS/EventBridge** (pub/sub
  fan-out) so a slow consumer never takes down producers."*

➡️ **Next:** [08 — Observability & security](./08_Observability_Security.md)
