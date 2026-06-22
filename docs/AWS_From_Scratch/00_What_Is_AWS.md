# 00 — What is AWS & the Cloud

## 🧠 The One Idea

**The cloud is electricity for computing: you plug in and pay for what you use, instead of
building your own power plant.** Before the cloud, you bought servers, racked them in a room,
cooled them, and over-provisioned "just in case." AWS owns that power plant; you rent capacity
**on demand**, scale it up in minutes, and turn it off when done. You stop buying hardware and
start renting **capabilities**.

The common one-liner: **"AWS is on-demand, pay-as-you-go cloud infrastructure."**

---

## 1. What "cloud computing" actually means

- **On-demand** — provision servers, storage, databases in **minutes via an API**, not weeks via
  purchase orders.
- **Pay-as-you-go** — pay per hour/second/request/GB; no big upfront capital. Turn it off →
  stop paying.
- **Elastic** — scale **out** under load and back **in** when quiet, automatically.
- **Managed** — AWS runs the undifferentiated heavy lifting (power, cooling, hardware, and for
  managed services, patching/backups) so you focus on your app.

---

## 2. The service models (IaaS / PaaS / SaaS)

Pizza analogy — how much do *you* make vs buy?

- **IaaS** (e.g. **EC2**) — AWS gives raw compute/network/storage; you manage the OS and up.
  *"Rent the kitchen."*
- **PaaS** (e.g. **RDS**, **Lambda**, **Elastic Beanstalk**) — AWS manages the platform; you
  bring code/data. *"Rent a kitchen with a chef."*
- **SaaS** (e.g. ready-made apps) — fully managed software you just use. *"Order the pizza."*

Higher up = less control, less ops burden. Most architectures mix all three.

---

## 3. The shared responsibility model (interviewers love this)

Security is **split**:

- **AWS = security *of* the cloud** — physical data centers, hardware, the hypervisor, the
  network backbone, and the managed-service internals.
- **You = security *in* the cloud** — your data, IAM permissions, OS patching (on EC2), network
  config (security groups), and **encryption** choices.

The line **moves** by service: with EC2 you patch the OS; with Lambda/RDS AWS does more of it.
**Misconfiguration on your side** (open S3 bucket, over-broad IAM) is the most common cause of
breaches — not AWS being hacked.

---

## 4. How you're billed (the mental model)

- You pay for **compute time**, **storage volume**, **data transfer out**, and **requests**.
- **Data transfer *in* is usually free; transfer *out* (egress) costs** — a classic surprise.
- Same resource can have multiple pricing modes (on-demand, reserved, spot — Lesson 09).
- **Tag everything** and use budgets/alerts so cost doesn't drift.

---

## 5. Why companies use AWS

- **Speed/agility** — launch in minutes, experiment cheaply, fail fast.
- **Elastic scale** — handle spikes without owning peak hardware year-round.
- **Global reach** — deploy near users worldwide (Lesson 01).
- **Breadth** — 200+ services so you rarely build plumbing from scratch.
- **Opex over capex** — no big upfront hardware spend.

---

## 🎤 Say this in the interview

- *"The cloud is on-demand, pay-as-you-go computing — I provision via API in minutes and scale
  elastically instead of buying hardware."*
- *"The **shared responsibility model**: AWS secures *of* the cloud (hardware, facilities); I
  secure *in* the cloud (data, IAM, patching, encryption) — and the line shifts by service."*
- *"Most breaches are **customer misconfiguration** — open buckets, over-broad IAM — so I start
  from least privilege."*

➡️ **Next:** [01 — Global infrastructure & the console](./01_Global_Infrastructure.md)
