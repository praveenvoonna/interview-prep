# 03 — Providers & AWS

## 🧠 The One Idea

**A provider is the plugin that teaches Terraform how to talk to one platform's API** — AWS, GCP,
Azure, Kubernetes, even GitHub. Terraform itself knows nothing about EC2; the **AWS provider**
translates your `resource` blocks into AWS API calls. This plugin model is exactly why Terraform is
cloud-agnostic.

The one-liner: **"Providers are plugins that map Terraform resources to a platform's API; that's how
one tool manages AWS, GCP, K8s, and more."**

---

## 1. Configuring a provider

```hcl
terraform {
  required_providers {
    aws = { source = "hashicorp/aws", version = "~> 5.0" }
  }
}

provider "aws" {
  region = "us-east-1"
  # creds come from env / ~/.aws / IAM role — never hardcode
}
```

`terraform init` downloads the provider plugin.

---

## 2. Resources vs data sources

- **`resource`** = something Terraform **creates and manages** (it owns its lifecycle).
- **`data`** = something that **already exists** and you only **read**.

```hcl
data "aws_ami" "ubuntu" {            # look up an existing AMI
  most_recent = true
  owners      = ["099720109477"]
  filter { name = "name", values = ["ubuntu/images/*-24.04-*"] }
}

resource "aws_instance" "web" {       # create a new instance using it
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t3.micro"
}
```

---

## 3. References & dependencies

- Referencing one resource's attribute in another (`aws_vpc.main.id`) creates an **implicit
  dependency** — Terraform builds them in the right order automatically.
- `depends_on` forces an explicit order when there's no attribute link.
- Terraform builds a **dependency graph** and parallelizes independent resources.

---

## 4. Inputs, locals, outputs

```hcl
variable "env" { default = "dev" }
locals { name = "myapp-${var.env}" }          # computed helper values

output "instance_ip" {
  value = aws_instance.web.public_ip          # surfaced after apply
}
```

- **variables** = external inputs, **locals** = internal computed values, **outputs** = exported
  results (also consumed by parent modules).

---

## 5. Credentials & safety

- **Never hardcode keys.** Use environment vars, the AWS credentials file, or an **IAM role**
  (instance profile / OIDC in CI).
- Scope IAM to **least privilege** for what Terraform manages.
- Remember state can hold sensitive attributes → encrypt remote state (Lesson 01).

---

## 🎤 Say this in the interview

- *"Providers are plugins mapping Terraform resources to a platform's API — that's why the same tool
  manages AWS, GCP, and Kubernetes."*
- *"`resource` is something Terraform creates and owns; `data` reads something that already exists,
  and attribute references build the dependency graph automatically."*
- *"I never hardcode credentials — env vars or IAM roles — and I keep the IAM scope least-privilege."*

➡️ **Next:** [04 — CloudFormation compared](./04_CloudFormation_Compared.md)
