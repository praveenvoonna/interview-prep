# 09 — Cost, Well-Architected & Gotchas

## 🧠 The One Idea

**On the cloud, architecture *is* the bill — every design choice has a price, so good design and
cost control are the same skill.** AWS gives you a scorecard for this (the **Well-Architected
Framework**) and a handful of pricing levers. Know the levers, the pillars, and the classic
money-and-design traps.

The common one-liner: **"Pay for what you use, right-size, and design against the
Well-Architected pillars."**

---

## 1. Pricing models (the levers)

- **On-Demand** — pay per second/hour, no commitment. Flexible, most expensive. Default for
  spiky/unknown load.
- **Savings Plans / Reserved Instances** — commit **1–3 years** for up to ~72% off. For
  **steady, predictable** baseline load.
- **Spot Instances** — spare capacity up to ~90% off, but AWS can **reclaim** with 2 min notice.
  For **fault-tolerant/batch/stateless** work.
- **Serverless (Lambda/Fargate)** — pure pay-per-use, scales to zero.

**Strategy:** Reserved/Savings for the steady **baseline**, On-Demand for **variable**, Spot for
**fault-tolerant** spikes.

---

## 2. Cost management tools & habits

- **Cost Explorer** (analyze/forecast spend), **Budgets** (alerts when spend crosses a threshold),
  **Cost Allocation Tags** (attribute cost to teams/projects).
- **Right-size** — most bills are over-provisioned instances; use Compute Optimizer.
- **S3 lifecycle** to Glacier; delete unattached EBS volumes & old snapshots; turn off idle
  dev/test (scheduled scaling).
- **Watch data-transfer-out (egress)** and **NAT Gateway** data charges — silent budget killers.

---

## 3. The Well-Architected Framework (6 pillars)

Name them — they're a common interview prompt:

1. **Operational Excellence** — run & monitor, automate, IaC, learn from failure.
2. **Security** — least privilege, encryption, traceability (Lessons 02, 08).
3. **Reliability** — multi-AZ, auto-recovery, backups, handle failure gracefully.
4. **Performance Efficiency** — right resource for the job, scale, use managed services.
5. **Cost Optimization** — pay for what you need; the levers above.
6. **Sustainability** — minimize environmental impact (efficient regions/usage).

---

## 4. Classic gotchas

- **Open S3 bucket** — public data leak. Fix: **Block Public Access**, bucket policies, no public
  ACLs.
- **Over-broad IAM** (`Action: "*"`, `Resource: "*"`) — blast radius. Fix: least privilege.
- **Single-AZ deployment** — one AZ outage = downtime. Fix: **multi-AZ** always.
- **Hardcoded credentials** in code/AMIs — use **roles**/Secrets Manager.
- **Surprise egress bill** — large data-transfer-out; design to keep traffic in-Region/use
  CloudFront.
- **Root account for daily work** — lock it down, MFA, use IAM identities.
- **No backups / untested restores** — enable automated backups *and* test recovery.
- **Forgetting Reserved/Savings on steady load** — leaving money on the table.

---

## 5. The reliability/cost balance (the senior framing)

*"I start from the **Well-Architected** pillars: multi-AZ for reliability, least-privilege +
encryption for security, managed services for efficiency, and a **pricing mix** (Savings Plans
baseline, On-Demand variable, Spot for fault-tolerant) for cost — with **tags, Budgets, and
right-sizing** to keep it honest."*

---

## 🎤 Say this in the interview

- *"Pricing levers: **On-Demand** (flexible), **Savings Plans/Reserved** (steady baseline, big
  discount), **Spot** (cheap, interruptible). I mix them by workload predictability."*
- *"I design against the **6 Well-Architected pillars** — operational excellence, security,
  reliability, performance, cost, sustainability."*
- *"Top gotchas I guard against: **open S3 buckets**, **over-broad IAM**, **single-AZ**, and
  **surprise egress** costs."*

➡️ **Next:** [10 — Hands-on lab](./10_Hands_On_Lab.md)
