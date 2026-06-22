# 04 — Compute: EC2, Containers & Lambda

## 🧠 The One Idea

**Compute is "where your code runs," and AWS offers it at three levels of how-much-do-I-manage:
rent a whole server (EC2), run containers (ECS/EKS/Fargate), or just hand over a function
(Lambda).** The more AWS manages, the less you operate — and the more you trade control for
convenience. Pick the level that matches the workload.

The common one-liner: **"EC2 = servers, containers = packaged apps, Lambda = functions —
decreasing ops, increasing abstraction."**

---

## 1. EC2 — virtual servers

- An **EC2 instance** is a virtual machine: you pick an **instance type** (CPU/RAM/network,
  e.g. `t3.medium`), an **AMI** (the OS image), storage (**EBS**), and a **security group**.
- **Instance families** target workloads: **t/m** general, **c** compute, **r/x** memory, **g/p**
  GPU. Mention "right-size to the workload."
- **You manage** the OS, patching, scaling, and availability. Most control, most ops.
- Made elastic with **Auto Scaling Groups** + **load balancers** (Lesson 07).

---

## 2. Containers — ECS, EKS, Fargate

- **Containers** package an app + deps to run consistently anywhere (Docker). Better density and
  faster start than full VMs.
- **ECS** — AWS's own container orchestrator (simpler, AWS-opinionated).
- **EKS** — managed **Kubernetes** (use when you want the K8s ecosystem/portability).
- **Fargate** — **serverless containers**: you give a task definition; AWS runs the containers
  with **no EC2 to manage**. Works under both ECS and EKS.
- **ECR** — the registry that stores your container images.

**EC2 launch type vs Fargate:** EC2 = you manage the underlying nodes (cheaper at scale, more
control); Fargate = no servers to manage (simpler, pay per task).

---

## 3. Lambda — serverless functions

- You upload a **function**; it runs **in response to events** (HTTP via API Gateway, S3 upload,
  SQS message, schedule…). No servers, no scaling config.
- **Pay per request + execution time** (ms). Scales to zero (no cost when idle) and up
  automatically to many concurrent executions.
- **Constraints to mention:** max execution timeout (15 min), memory-linked CPU, package-size
  limits, and **cold starts** (first invoke latency). Great for event-driven/glue/spiky work;
  not for long-running or very latency-sensitive constant load.

---

## 4. How to choose (the decision answer)

| Workload | Pick |
|---|---|
| Legacy app, full OS control, steady load | **EC2** |
| Microservices already containerized | **ECS/EKS** (Fargate if you don't want nodes) |
| Event-driven, spiky, short tasks, glue code | **Lambda** |
| Want zero server management | **Fargate** or **Lambda** |

Trade-off sentence: *"Higher abstraction = less operational burden but less control and
potential cold-start/limits — I match the level to the workload."*

---

## 5. Pricing models (cross-cutting — see Lesson 09)

- **On-Demand** — pay per second/hour, no commitment. Default.
- **Reserved Instances / Savings Plans** — commit 1–3 yrs for big discounts on steady load.
- **Spot** — spare capacity up to ~90% off, but can be **reclaimed** — for fault-tolerant/batch.
- Lambda/Fargate are inherently pay-per-use.

---

## 🎤 Say this in the interview

- *"Three compute levels: **EC2** (full servers, most control), **containers** via ECS/EKS (with
  **Fargate** for serverless containers), and **Lambda** (functions, event-driven, scale to
  zero)."*
- *"I pick by workload: steady/legacy → EC2 with Auto Scaling; microservices → ECS/EKS;
  spiky/glue → Lambda. Higher abstraction trades control for less ops."*
- *"For cost I mix **On-Demand**, **Savings Plans/Reserved** for steady load, and **Spot** for
  fault-tolerant batch."*

➡️ **Next:** [05 — Storage: S3, EBS & EFS](./05_Storage.md)
