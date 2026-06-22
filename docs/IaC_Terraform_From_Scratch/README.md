# 🏗️ IaC & Terraform From Scratch — A Crash Course (in plain English)

A from-zero course on **Infrastructure as Code** — Terraform first, with a side-by-side on
**CloudFormation** (interviews often ask about both). Manage
infrastructure the way you manage code: declared, versioned, reviewable.

> **How to read this:** go in order, 00 → 06. Each lesson starts with a plain-English analogy
> (**"The One Idea"**), then the real mechanics, then **"Say this in the interview"** lines.

> **Status:** 📝 *Syllabus ready — lessons to be written.* This README is the outline and the
> reference you can study from today.

---

## 📂 The lessons (planned, read in order)

### Part A — The idea
- **00 — Why IaC** — drift, reproducibility, code review for infra, declarative vs imperative.

### Part B — Terraform core
- **01 — Core workflow & state** — `init/plan/apply`, the state file, why state matters, remote
  state & locking.
- **02 — Modules** — reusable components, inputs/outputs, composition.
- **03 — Providers & AWS** — resources, data sources, provisioning real AWS infra.

### Part C — The comparison
- **04 — CloudFormation compared** — stacks, change sets, templates vs HCL, when each is chosen.

### Part D — Practice
- **05 — Hands-on lab** — provision a small AWS stack (VPC + EC2/S3) with Terraform.
- **06 — Interview Q&A flashcards** — state/modules/CFN-vs-TF rapid-fire.

---

## 🧠 The whole course in three sentences
1. **IaC makes infrastructure declarative and reviewable:** you describe the desired state and
   the tool computes the diff to get there.
2. **State is Terraform's source of truth:** it maps your config to real resources, so remote,
   locked state is essential for teams.
3. **Terraform is cloud-agnostic via providers; CloudFormation is AWS-native:** the trade-off is
   portability and ecosystem vs deep, first-party AWS integration.

---

## 🔗 Companion material
- **[AWS From Scratch](../AWS_From_Scratch/)** — the resources you'll provision.
- **[Docker From Scratch](../Docker_From_Scratch/)** — pairs with IaC for full delivery.

When the lessons are written, start at **00**.
