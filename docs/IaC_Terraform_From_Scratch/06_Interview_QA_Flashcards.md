# 06 — Interview Q&A Flashcards

Rapid-fire. Cover the answer, say it aloud, then check. Read twice.

---

## IaC basics

**Q: What is IaC?**
Describing infrastructure as versioned, reviewable code; the tool computes the diff to reach the
desired state.

**Q: Declarative vs imperative?**
Declarative = describe the end state (Terraform); imperative = describe the steps (a script).

**Q: What problems does it solve?**
Drift, unrepeatable click-ops, no history/rollback, undocumented infra.

---

## Workflow & state

**Q: The core workflow?**
`init` → `plan` → `apply` (and `destroy`); always review the plan diff first.

**Q: What is the state file?**
Terraform's map from config to real resource IDs — how it computes diffs and tracks dependencies.

**Q: Why remote state + locking?**
Shared, durable, encrypted state for teams; locking serializes applies so state isn't corrupted.

**Q: What's in state that's sensitive?**
Resource attributes can include secrets (e.g. passwords) → encrypt it, restrict access.

---

## Modules

**Q: What is a module?**
A reusable, parameterized bundle of resources — variables in, outputs out.

**Q: Why use modules?**
DRY, consistency across environments, encapsulation, versioning.

**Q: Where can a module source from?**
Local path, Git URL, or the Terraform Registry.

---

## Providers & resources

**Q: What is a provider?**
A plugin mapping Terraform resources to a platform's API (AWS, GCP, K8s) — the basis of multi-cloud.

**Q: resource vs data source?**
`resource` = Terraform creates/owns it; `data` = reads something that already exists.

**Q: How are dependencies determined?**
Implicitly from attribute references (`aws_vpc.main.id`), or explicitly with `depends_on`.

**Q: How should credentials be handled?**
Env vars or IAM roles — never hardcoded — with least-privilege scope.

---

## Terraform vs CloudFormation

**Q: Biggest difference?**
Terraform is multi-cloud and you manage state; CloudFormation is AWS-native and AWS manages state.

**Q: Plan equivalent in CFN?**
Change sets.

**Q: Rollback behavior?**
CFN auto-rolls-back a failed stack update; Terraform leaves it partial — you fix forward.

**Q: What is AWS CDK?**
Real code (TS/Python) that synthesizes CloudFormation templates.

**Q: When pick which?**
CFN/CDK for all-AWS with managed state; Terraform for multi-cloud or one consistent workflow.

---

🎉 **You finished IaC & Terraform From Scratch.** Lead with the three-sentence summary: **declarative
+ reviewable**, **state is the source of truth (remote + locked)**, **Terraform multi-cloud vs CFN
AWS-native**. Be ready to read a `plan` and explain update-in-place vs replace.
