# 01 — Global Infrastructure & the Console

## 🧠 The One Idea

**AWS is a worldwide chain of data centers grouped into Regions, and each Region has several
independent buildings (Availability Zones) so one fire doesn't take you down.** You choose
*where* your stuff lives (a Region near your users/laws), and you spread copies across AZs for
resilience. Edge locations push content even closer to users. Geography is a first-class design
decision.

The common one-liner: **"Region = a geographic area; AZ = an isolated data center within it;
spread across AZs for HA."**

---

## 1. Regions

- A **Region** is a geographic location (e.g. `us-east-1` N. Virginia, `eu-west-1` Ireland,
  `ap-south-1` Mumbai). 30+ worldwide.
- **Why you pick one:** **latency** (near users), **compliance/data residency** (GDPR etc.),
  **service availability** (new services land in big Regions first), and **price** (varies by
  Region).
- Most resources are **Region-scoped** — an S3 bucket or VPC lives in one Region. A few services
  are **global** (IAM, Route 53, CloudFront).

---

## 2. Availability Zones (AZs)

- Each Region has **multiple AZs** (typically 3+) — **physically separate** data centers with
  independent power, cooling, and networking, but linked by low-latency private fiber.
- **The HA rule:** deploy across **≥2 AZs** so a single-AZ failure doesn't kill your app. Load
  balancers, RDS Multi-AZ, and Auto Scaling all build on this.
- **One picture:** Region = a city; AZs = several buildings in that city far enough apart to fail
  independently, close enough to sync fast.

---

## 3. Edge locations & global reach

- **Edge locations** (hundreds, more than Regions) cache content close to users for
  **CloudFront** (CDN) and serve **Route 53** DNS and **AWS Global Accelerator**.
- Purpose: cut latency for end users worldwide without putting your whole app everywhere.

---

## 4. How you interact with AWS

- **Console** — the web UI; great for learning and one-off tasks, not for repeatability.
- **CLI** (`aws ...`) — scripts and automation from a terminal.
- **SDKs** — call AWS from code (Go, Python/boto3, JS, Java…).
- **Infrastructure as Code (IaC)** — **CloudFormation** (AWS-native) or **Terraform**
  (multi-cloud): declare resources in files, version them, recreate environments reliably.
  *Prefer IaC over click-ops* for anything real — say this in interviews.

All four ultimately call the **same AWS APIs**.

---

## 5. Choosing where to deploy (a quick checklist)

- **Latency** → Region closest to most users (or Global Accelerator/CloudFront for edge).
- **Compliance** → Region that satisfies data-residency laws.
- **Resilience** → ≥2 AZs always; multi-Region only for serious DR/global apps (more cost/
  complexity).
- **Cost** → compare Region pricing; cheaper Regions exist.

---

## 🎤 Say this in the interview

- *"A **Region** is a geographic area; each has multiple isolated **Availability Zones**. I pick
  a Region for latency, compliance, and cost, and I deploy across **≥2 AZs** for high
  availability."*
- *"**Edge locations** power CloudFront and Route 53 to cut user latency globally without
  replicating the whole app."*
- *"I drive AWS with **IaC** (CloudFormation/Terraform), not click-ops, so environments are
  versioned and reproducible."*

➡️ **Next:** [02 — IAM: identity & access](./02_IAM.md)
