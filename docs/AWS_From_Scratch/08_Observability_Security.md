# 08 — Observability & Security

## 🧠 The One Idea

**Observability answers "what is my system doing?" and security answers "who did what, and is my
data protected?"** CloudWatch is the dashboard (metrics, logs, alarms); CloudTrail is the
security camera (who called which API); KMS/encryption is the safe. You can't operate or pass an
audit without these three.

The common one-liner: **"CloudWatch = monitoring, CloudTrail = audit, KMS = encryption."**

---

## 1. CloudWatch — monitoring & operations

- **Metrics** — numeric time-series (CPU, latency, queue depth). Most services publish them
  automatically; you can push **custom metrics**.
- **Logs** — central log storage (app logs, Lambda logs, VPC Flow Logs). Query with **Logs
  Insights**.
- **Alarms** — fire when a metric crosses a threshold → notify (SNS), trigger Auto Scaling, or
  run an action.
- **Dashboards** — visualize it all. **X-Ray** adds **distributed tracing** across services to
  find latency bottlenecks.

*The triad: **metrics** (numbers), **logs** (text), **traces** (request paths).*

---

## 2. CloudTrail — audit who-did-what

- Records **every AWS API call** (who, when, from where, what action) across the account.
- For **security forensics, compliance, and change tracking** — "who deleted that bucket?"
- Best practice: enable across all Regions, deliver to a locked-down S3 bucket.

**CloudWatch vs CloudTrail (classic question):** CloudWatch = **performance/operational**
telemetry ("is it healthy?"); CloudTrail = **audit log of API activity** ("who did it?").

---

## 3. Other security services to name

- **AWS Config** — tracks resource **configuration** over time and checks **compliance** against
  rules ("is anything non-compliant?").
- **GuardDuty** — intelligent **threat detection** from logs (anomalies, malicious IPs).
- **WAF** — web application firewall (blocks SQLi/XSS, rate limiting) in front of ALB/CloudFront.
- **Shield** — DDoS protection. **Security Hub** — aggregates findings.
- **Secrets Manager / Parameter Store** — store DB passwords/API keys securely (rotation), never
  in code.

---

## 4. Encryption & KMS

- **KMS (Key Management Service)** — creates and manages **encryption keys**; most services
  integrate with it for one-click encryption.
- **At rest:** encrypt S3, EBS, RDS, DynamoDB with KMS keys (often on by default now).
- **In transit:** **TLS/HTTPS** everywhere.
- **Customer-managed keys (CMK)** give you control over rotation and **who can use the key** (key
  policies) — useful for compliance and separation of duties.

**Encryption everywhere is the default expectation** — say "at rest with KMS, in transit with
TLS."

---

## 5. A practical security checklist

- Least-privilege **IAM**, MFA, no root usage (Lesson 02).
- **Encrypt** at rest (KMS) and in transit (TLS).
- **Private subnets** for app/DB; security groups tight (Lesson 03).
- **CloudTrail** on everywhere; **GuardDuty** + **Config** enabled.
- Secrets in **Secrets Manager**, never in code/AMIs.
- **Block Public Access** on S3.

---

## 🎤 Say this in the interview

- *"Observability triad: **metrics, logs, traces** — CloudWatch for metrics/logs/alarms, **X-Ray**
  for tracing."*
- *"**CloudWatch** is operational health; **CloudTrail** is the audit log of every API call — who
  did what. I keep CloudTrail on across all Regions."*
- *"Encrypt **at rest with KMS** and **in transit with TLS**, keep secrets in **Secrets Manager**,
  and layer **GuardDuty/Config/WAF** for detection and protection."*

➡️ **Next:** [09 — Cost, well-architected & gotchas](./09_Cost_And_Gotchas.md)
