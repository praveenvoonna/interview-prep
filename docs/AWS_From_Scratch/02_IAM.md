# 02 — IAM: Identity & Access

## 🧠 The One Idea

**IAM is the bouncer and rulebook for your AWS account: it decides *who* can do *what* to *which*
resource.** Every API call is checked against policies. The golden rule is **least privilege** —
hand out the smallest set of permissions that gets the job done, and prefer **temporary** badges
(roles) over permanent keys (long-lived passwords/keys).

The common one-liner: **"IAM controls authentication and authorization for every AWS API call,
default-deny."**

---

## 1. The four building blocks

- **Users** — a person or app with long-term credentials (password, access keys). Use sparingly.
- **Groups** — a bucket of users sharing policies (e.g. `Developers`). Attach policies to the
  group, not each user.
- **Roles** — an identity with **temporary** credentials that anything trusted can **assume**
  (an EC2 instance, a Lambda, a user, another account). **The preferred way** to grant access.
- **Policies** — JSON documents listing permissions (`Effect`, `Action`, `Resource`,
  optional `Condition`). Attached to users/groups/roles.

---

## 2. How an authorization decision is made

1. **Default deny** — everything is denied unless explicitly allowed.
2. An **explicit Allow** in any applicable policy grants the action…
3. …**unless** an **explicit Deny** matches — **Deny always wins**.

```json
{ "Effect": "Allow",
  "Action": ["s3:GetObject"],
  "Resource": "arn:aws:s3:::my-bucket/*" }
```

So: *deny by default → allow grants → explicit deny overrides.* Memorize that order.

---

## 3. Roles — why they beat access keys

- A role gives **short-lived, auto-rotating** credentials via STS — no secrets to leak or rotate
  manually.
- **EC2/Lambda get an instance/execution role** instead of you baking access keys into code.
  *Never put long-lived keys in code or AMIs* — say this.
- **Cross-account access** and federation (SSO/IdP, IAM Identity Center) work by **assuming
  roles**.

---

## 4. Policy types you should name

- **Identity-based** — attached to users/groups/roles (the common case).
- **Resource-based** — attached to the resource (e.g. an **S3 bucket policy**, SQS/SNS policy);
  can grant cross-account access.
- **Managed vs inline** — **managed** policies (AWS- or customer-managed) are reusable and
  preferred; **inline** is embedded in one identity (use rarely).
- **Permission boundaries** & **SCPs** (Organizations) — *guardrails* that cap the **maximum**
  permissions, even if an identity policy allows more.

---

## 5. Best practices (the answer they want)

- **Least privilege** — start minimal, add as needed; use Access Analyzer to trim.
- **Lock down the root user** — MFA, no daily use, no access keys.
- **MFA everywhere** for humans.
- **Roles over users/keys**; rotate any keys that must exist.
- **Groups for humans, roles for workloads.**
- **No wildcards** like `"Action": "*"` / `"Resource": "*"` in production.

---

## 🎤 Say this in the interview

- *"IAM is **default-deny**: an explicit Allow grants an action unless an explicit Deny overrides
  it — Deny always wins."*
- *"I prefer **roles** with temporary STS credentials over long-lived access keys; EC2/Lambda get
  an attached role so no secrets live in code."*
- *"I design for **least privilege**, lock down root with MFA, use groups for people and roles for
  workloads, and add **SCPs/permission boundaries** as guardrails."*

➡️ **Next:** [03 — Networking: VPC](./03_VPC_Networking.md)
