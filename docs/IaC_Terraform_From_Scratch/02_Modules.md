# 02 — Modules

## 🧠 The One Idea

**A module is a reusable function for infrastructure: you pass inputs, it builds a bundle of
resources, and it hands back outputs.** Instead of copy-pasting the same VPC + subnets + security
groups everywhere, you write it once as a module and call it with different parameters per
environment.

The one-liner: **"Modules are reusable, parameterized bundles of resources — inputs in, outputs out
— so you stop copy-pasting infra."**

---

## 1. Every config is already a module

- The directory you run `terraform apply` in is the **root module**.
- Any directory of `.tf` files can be **called** as a **child module**.
- Modules **compose**: root calls child calls grandchild.

---

## 2. The three pieces of a module

```hcl
# variables.tf — inputs
variable "instance_count" { type = number, default = 2 }
variable "name"           { type = string }

# main.tf — resources
resource "aws_instance" "web" {
  count         = var.instance_count
  ami           = "ami-123"
  instance_type = "t3.micro"
  tags = { Name = "${var.name}-${count.index}" }
}

# outputs.tf — outputs
output "ids" { value = aws_instance.web[*].id }
```

**Variables = parameters, outputs = return values, resources = the body.**

---

## 3. Calling a module

```hcl
module "web_cluster" {
  source         = "./modules/web"   # local path, Git URL, or registry
  name           = "prod-web"
  instance_count = 5
}

# use its outputs elsewhere
resource "aws_lb_target_group_attachment" "a" {
  for_each = toset(module.web_cluster.ids)
  target_id = each.value
}
```

Source can be a **local path**, a **Git repo**, or the **Terraform Registry** (community/official
modules).

---

## 4. Why modules matter

- **DRY:** one definition, many uses (dev/staging/prod with different inputs).
- **Consistency:** every environment built the same way → less drift.
- **Encapsulation:** hide complexity behind a clean input/output interface.
- **Versioning:** pin a module version so upgrades are deliberate.

---

## 5. Good module practice

- Keep modules **small and focused** (a "network" module, a "database" module) — not one mega-module.
- **Pin versions** (`version = "~> 3.1"` from a registry/Git tag) for reproducibility.
- Expose only the inputs callers need; give sensible **defaults**.
- Use `for_each`/`count` for repetition, and **outputs** to wire modules together.

---

## 🎤 Say this in the interview

- *"A module is a reusable, parameterized bundle of resources — variables are its inputs, outputs its
  return values — so I define infra like a VPC once and reuse it per environment."*
- *"Modules give DRY, consistency, and encapsulation; I keep them small and focused and pin their
  versions."*
- *"The root directory is itself a module; sources can be local paths, Git, or the Terraform
  Registry."*

➡️ **Next:** [03 — Providers & AWS](./03_Providers_And_AWS.md)
