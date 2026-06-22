# ☁️ AWS From Scratch — A Crash Course (in plain English)

A from-zero deep dive into **Amazon Web Services**: *what the cloud is, the core building blocks,
and how they fit together* — explained in **naive, everyday language** with analogies, then the
real mechanics, then **interview-ready lines**.

> **How to read this:** go in order, 00 → 11. Each lesson starts with a plain-English analogy
> (**"The One Idea"**), then the real details, then **"Say this in the interview"** one-liners
> you can repeat almost verbatim. Don't skip the analogies — they're the hooks that make the
> rest stick.

---

## 📂 The lessons (read in order)

### Part A — The mental model (don't skip)
- **[00 — What is AWS & the cloud](./00_What_Is_AWS.md)** — cloud vs on-prem, the shared
  responsibility model, how you're billed.
- **[01 — Global infrastructure & the console](./01_Global_Infrastructure.md)** — Regions, AZs,
  edge locations; how you interact (Console/CLI/SDK/IaC).

### Part B — The foundation (the 90% you always touch)
- **[02 — IAM: identity & access](./02_IAM.md)** — users, roles, policies, the
  least-privilege model.
- **[03 — Networking: VPC](./03_VPC_Networking.md)** — subnets, route tables, gateways,
  security groups vs NACLs.
- **[04 — Compute: EC2, containers & Lambda](./04_Compute.md)** — instances, ECS/EKS/Fargate,
  serverless.

### Part C — Storage & data
- **[05 — Storage: S3, EBS & EFS](./05_Storage.md)** — object vs block vs file; S3 classes &
  durability.
- **[06 — Databases: RDS, DynamoDB & friends](./06_Databases.md)** — relational vs NoSQL,
  caching, when to use which.

### Part D — Glue, scale & operations
- **[07 — Scaling & load balancing](./07_Scaling_And_LB.md)** — ELB, Auto Scaling, decoupling
  with SQS/SNS.
- **[08 — Observability & security](./08_Observability_Security.md)** — CloudWatch, CloudTrail,
  KMS, encryption.
- **[09 — Cost, well-architected & gotchas](./09_Cost_And_Gotchas.md)** — pricing models, the
  pillars, classic mistakes.

### Part E — Practice
- **[10 — Hands-on lab](./10_Hands_On_Lab.md)** — build a small VPC + EC2 + S3 setup with the
  CLI so it becomes real.
- **[11 — Interview Q&A flashcards](./11_Interview_QA_Flashcards.md)** — rapid-fire questions
  with crisp answers.

---

## 🧠 The whole course in three sentences

1. **The cloud is renting someone else's data center on demand:** you pay for what you use, and
   AWS runs the hardware while **you** secure what you put on it (shared responsibility).
2. **Almost everything sits inside a VPC and is gated by IAM:** networking defines *where* things
   can talk, IAM defines *who* can do *what* — get these two right and the rest is services.
3. **Pick the right building block per job:** EC2/containers/Lambda for compute, S3/EBS/EFS for
   storage, RDS/DynamoDB for data, and ELB + Auto Scaling to make it elastic.

If you can say those three sentences and explain each, you've got the backbone.

---

## 🛠️ Tools for the lab (Lesson 10)
- **An AWS account** (free tier covers the lab) and the **AWS CLI v2** configured with a
  least-privilege IAM user/role.
- (Optional) **Terraform** or **CloudFormation** to see infrastructure-as-code.

You've got this. Start at **[00](./00_What_Is_AWS.md)**.
