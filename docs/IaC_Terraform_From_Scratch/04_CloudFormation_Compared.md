# 04 — CloudFormation Compared

> *Interviews often ask about both — be ready to contrast them crisply.*

## 🧠 The One Idea

**CloudFormation is AWS's own built-in IaC; Terraform is the cloud-agnostic third party.** They do
the same job — declare resources, compute a diff, apply it — but CFN is deeply native to AWS (and
only AWS), while Terraform speaks to every cloud through providers. The choice is **portability +
ecosystem vs first-party AWS integration**.

The one-liner: **"Terraform is multi-cloud with provider plugins and external state; CloudFormation
is AWS-native, manages its own state, and integrates first-class with AWS."**

---

## 1. Side by side

| | Terraform | CloudFormation |
|---|---|---|
| Scope | multi-cloud (providers) | AWS only |
| Language | **HCL** | JSON / **YAML** |
| State | **you manage** (S3/Cloud) | AWS manages it for you |
| Unit | modules | **stacks** / nested stacks |
| Preview | `terraform plan` | **change sets** |
| Drift | `plan` detects | drift detection feature |
| Rollback | manual (re-apply) | **automatic rollback** on failure |
| Ecosystem | huge module registry | AWS-native + SAM/CDK |

---

## 2. Stacks vs state

- **CFN:** resources are grouped into a **stack**; AWS tracks state **for you** (no state file to
  babysit, no locking to set up).
- **Terraform:** you own the **state file** and its backend/locking — more setup, but full control
  and visibility.

---

## 3. Preview & rollback

- **Change sets** (CFN) ≈ `terraform plan`: preview before applying.
- CFN does **automatic rollback** if a stack update fails — it reverts to the last good state.
- Terraform leaves a failed apply partially applied; you fix forward and re-apply. (More control,
  less hand-holding.)

---

## 4. When each is chosen

- **CloudFormation / CDK:** all-in on AWS, want managed state and tight AWS integration (e.g. SAM
  for serverless), org mandates AWS-native tooling.
- **Terraform:** multi-cloud or hybrid, want one tool/workflow across providers, value the large
  module ecosystem and explicit plan/state.
- **AWS CDK** is worth a mention: real code (TypeScript/Python) that **synthesizes CloudFormation**.

---

## 5. What's common to both

- **Declarative desired state** + diff + apply.
- **Dependency ordering** from references.
- **Parameterization** (variables / CFN parameters) and **outputs**.
- Same discipline: review the diff, version the templates, least-privilege creds.

---

## 🎤 Say this in the interview

- *"Both are declarative IaC; CloudFormation is AWS-native and manages its own state with automatic
  rollback, while Terraform is multi-cloud via providers and I manage the state file myself."*
- *"CFN uses stacks and change sets; Terraform uses modules and `plan` — same idea, different
  packaging."*
- *"I'd pick CFN/CDK for an all-AWS shop wanting managed state, and Terraform for multi-cloud or one
  consistent workflow across providers."*

➡️ **Next:** [05 — Hands-on lab](./05_Hands_On_Lab.md)
