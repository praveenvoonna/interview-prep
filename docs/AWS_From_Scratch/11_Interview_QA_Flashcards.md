# 11 — Interview Q&A Flashcards

Rapid-fire. Cover the answer, say it aloud, then check. Read twice.

---

## Fundamentals

**Q: What is AWS / cloud computing?**
On-demand, pay-as-you-go computing provisioned via API in minutes; elastic and managed, so you
rent capabilities instead of buying hardware.

**Q: Explain the shared responsibility model.**
AWS secures *of* the cloud (hardware, facilities, managed-service internals); you secure *in* the
cloud (data, IAM, OS patching on EC2, network config, encryption). The line shifts by service.

**Q: IaaS vs PaaS vs SaaS?**
IaaS = raw infra (EC2, you manage OS up); PaaS = managed platform (RDS/Lambda, bring code/data);
SaaS = ready apps. Higher = less control, less ops.

---

## Global infra

**Q: Region vs Availability Zone?**
Region = a geographic area; AZ = a physically isolated data center within it. Deploy across ≥2 AZs
for high availability.

**Q: When pick a Region?**
Latency to users, compliance/data residency, service availability, and price.

---

## IAM

**Q: How does an IAM decision work?**
Default deny → an explicit Allow grants → an explicit Deny overrides everything (Deny wins).

**Q: Why roles over access keys?**
Roles give temporary, auto-rotating STS credentials — no long-lived secrets to leak. EC2/Lambda
assume a role so no keys live in code.

**Q: Security group vs NACL?**
SG = stateful, instance-level, allow-only. NACL = stateless, subnet-level, allow+deny, ordered.

---

## Networking

**Q: Public vs private subnet?**
Public has a route to an Internet Gateway; private doesn't. Private uses a **NAT gateway** for
outbound-only internet. DBs go in private subnets.

**Q: IGW vs NAT gateway?**
IGW = two-way internet for public subnets. NAT = outbound-only for private subnets (not reachable
inbound).

---

## Compute

**Q: EC2 vs containers vs Lambda?**
EC2 = full servers (most control/ops). Containers (ECS/EKS, Fargate serverless) = packaged apps.
Lambda = event-driven functions, scale to zero. Higher abstraction = less ops.

**Q: EC2 launch type vs Fargate?**
EC2 = you manage the nodes; Fargate = no servers to manage, pay per task.

**Q: When NOT to use Lambda?**
Long-running (>15 min), constant heavy load, or latency-critical paths where cold starts hurt.

---

## Storage

**Q: S3 vs EBS vs EFS?**
S3 = object (HTTP, 11 nines, lifecycle to Glacier). EBS = block disk for one EC2 (single-AZ,
snapshots to S3). EFS = shared NFS file system many instances mount across AZs.

**Q: S3 durability & a key safety feature?**
11 nines durability (across AZs); Block Public Access + bucket policies + versioning + SSE-KMS.

---

## Databases

**Q: RDS Multi-AZ vs read replica?**
Multi-AZ = standby in another AZ for **HA failover**. Read replica = async copy to **scale reads**.
Different purposes.

**Q: When DynamoDB vs RDS?**
DynamoDB for key-based lookups at massive scale, serverless, known access patterns. RDS for
relational data, joins, transactions.

**Q: How to cut DB latency/load?**
ElastiCache (Redis) with cache-aside.

---

## Scaling & integration

**Q: ALB vs NLB?**
ALB = Layer 7 (HTTP, path/host routing). NLB = Layer 4 (TCP/UDP, ultra-fast, static IPs).

**Q: What does Auto Scaling do?**
Keeps a fleet between min/desired/max, replaces unhealthy instances, scales on policies (target
tracking is the common one).

**Q: SQS vs SNS?**
SQS = queue (async point-to-point buffer, DLQs). SNS = pub/sub fan-out (one→many). Combine: SNS →
multiple SQS queues.

---

## Observability & security

**Q: CloudWatch vs CloudTrail?**
CloudWatch = metrics/logs/alarms (is it healthy?). CloudTrail = audit log of every API call (who
did what?).

**Q: Encryption defaults?**
At rest with **KMS**, in transit with **TLS**; secrets in Secrets Manager, never in code.

---

## Cost & design

**Q: Pricing models?**
On-Demand (flexible), Savings Plans/Reserved (steady baseline, big discount), Spot (cheap,
interruptible). Mix by predictability.

**Q: Name the Well-Architected pillars.**
Operational Excellence, Security, Reliability, Performance Efficiency, Cost Optimization,
Sustainability.

**Q: Top AWS gotchas?**
Open S3 buckets, over-broad IAM, single-AZ deployments, hardcoded credentials, surprise egress
costs.

---

🎉 **You finished AWS From Scratch.** Re-read the three-sentence summary in the
[README](./README.md), then say the **shared responsibility model**, **Region/AZ**, and the
**IAM default-deny** rule out loud — those carry the most weight.
