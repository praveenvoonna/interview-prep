# 01 — Core Workflow & State

## 🧠 The One Idea

**Terraform keeps a notebook (the state file) of everything it built, then compares your code to that
notebook to decide what to change.** The workflow is always the same: `init` (get plugins), `plan`
(show the diff), `apply` (make it real). State is what makes the diff possible — lose it and
Terraform forgets what it owns.

The one-liner: **"`init` → `plan` → `apply`; state is Terraform's record of real resources, so it
must be remote and locked for teams."**

---

## 1. The workflow

| Command | Does |
|---|---|
| `terraform init` | download providers/modules, set up the backend |
| `terraform plan` | compute & **show the diff** (create/change/destroy) — no changes made |
| `terraform apply` | execute the plan, update state |
| `terraform destroy` | tear down everything in state |

**Always read the `plan`** before `apply` — it's the safety net.

---

## 2. A minimal config

```hcl
terraform {
  required_providers { aws = { source = "hashicorp/aws", version = "~> 5.0" } }
}
provider "aws" { region = "us-east-1" }

resource "aws_s3_bucket" "data" {
  bucket = "example-app-data"
}
```

`resource "type" "name" { ... }` declares one piece of infrastructure.

---

## 3. What state is

- The **state file** (`terraform.tfstate`) maps your config (`aws_s3_bucket.data`) to the **real
  resource ID** in the cloud.
- It's how Terraform knows a resource already exists (update) vs is new (create).
- It also caches attributes and tracks **dependencies** between resources.

**Without state, Terraform can't tell what it already manages.**

---

## 4. Why remote state + locking

- Local state on one laptop = can't collaborate, easy to lose.
- **Remote backend** (S3 + DynamoDB lock, Terraform Cloud, GCS): shared, durable, versioned.
- **State locking** prevents two people running `apply` at once and corrupting state (the DynamoDB
  table holds the lock).
- State can contain **secrets** (e.g. DB passwords) → store it encrypted, restrict access.

---

## 5. Handy state operations

- `terraform plan` flags **drift** (someone changed infra by hand).
- `terraform import` adopts an existing resource into state.
- `terraform state mv/rm` refactors or forgets a resource.
- Use **workspaces** or separate state per environment to isolate dev/staging/prod.

---

## 🎤 Say this in the interview

- *"The loop is `init`, `plan`, `apply` — and I always review the plan diff before applying."*
- *"State is Terraform's map from config to real resource IDs; it's how it computes the diff and
  tracks dependencies."*
- *"For teams I use a remote backend with locking — S3 plus a DynamoDB lock table — so applies are
  serialized and state is durable and encrypted."*

➡️ **Next:** [02 — Modules](./02_Modules.md)
