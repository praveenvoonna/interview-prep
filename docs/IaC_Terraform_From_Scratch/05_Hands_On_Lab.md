# 05 — Hands-On Lab

## 🧠 The One Idea

**Provision a small real stack — a VPC and an S3 bucket — then watch `plan` predict and `apply` build
it.** Doing the full `init → plan → apply → destroy` loop once makes the whole tool click.

The one-liner: **"One small stack through the full lifecycle teaches state, diffs, and dependencies
better than any reading."**

---

## 1. Project layout

```
.
├── main.tf
├── variables.tf
└── outputs.tf
```

---

## 2. `variables.tf`

```hcl
variable "region"      { default = "us-east-1" }
variable "bucket_name" { default = "example-tf-lab-demo" }
```

---

## 3. `main.tf`

```hcl
terraform {
  required_providers { aws = { source = "hashicorp/aws", version = "~> 5.0" } }
}
provider "aws" { region = var.region }

resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
  tags = { Name = "tf-lab-vpc" }
}

resource "aws_subnet" "public" {
  vpc_id     = aws_vpc.main.id          # implicit dependency on the VPC
  cidr_block = "10.0.1.0/24"
}

resource "aws_s3_bucket" "data" {
  bucket = var.bucket_name
}
```

---

## 4. `outputs.tf`

```hcl
output "vpc_id"     { value = aws_vpc.main.id }
output "bucket_arn" { value = aws_s3_bucket.data.arn }
```

---

## 5. Run the lifecycle

```bash
terraform init      # download AWS provider
terraform plan      # review: "+ 3 to add" (vpc, subnet, bucket)
terraform apply     # type yes → resources created, state written
terraform show      # inspect state
terraform destroy   # clean up so you aren't billed
```

**Watch for:** the subnet waits on the VPC (dependency from `aws_vpc.main.id`), and `plan` shows the
exact diff before anything changes.

**Extensions:** move state to an **S3 backend with DynamoDB locking**; refactor the VPC+subnet into a
**module**; add a `count`/`for_each` for multiple subnets; change a value and re-`plan` to see an
**update in place** vs **replace**.

---

## 🎤 Say this in the interview

- *"I've run the full loop — `init`, `plan`, `apply`, `destroy` — and the plan diff plus dependency
  ordering (subnet waiting on the VPC) is exactly what makes Terraform safe."*
- *"I'd then move state to an S3 backend with a DynamoDB lock and refactor repeated resources into a
  module."*
- *"Reading `plan` for update-in-place vs replace is how I avoid accidental destroys in prod."*

➡️ **Next:** [06 — Interview Q&A flashcards](./06_Interview_QA_Flashcards.md)
