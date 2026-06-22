# 00 — Why IaC

## 🧠 The One Idea

**Infrastructure as Code means you describe your servers, networks, and databases in a text file and
let a tool build them — instead of clicking around a console.** The file is the source of truth:
versioned in Git, reviewed in a PR, and applied identically to dev, staging, and prod. No more "what
did someone change at 2am?"

The one-liner: **"IaC makes infrastructure declarative, versioned, and reviewable — you describe the
desired state and the tool computes the diff to reach it."**

---

## 1. The problem it solves

- **Click-ops** (manual console changes) is unrepeatable, undocumented, and error-prone.
- **Drift:** the real infrastructure slowly diverges from what anyone remembers configuring.
- **No history:** you can't `git blame` a console click or roll it back.

IaC turns infra into artifacts you treat exactly like application code.

---

## 2. Declarative vs imperative

- **Imperative** (a bash script): *the steps* — "create this, then that". You manage order and
  current state yourself.
- **Declarative** (Terraform): *the desired end state* — "I want one VPC and two instances". The
  tool figures out what to create/change/destroy to get there.

Terraform is **declarative**: you describe the *what*, it computes the *how*.

---

## 3. What you gain

- **Reproducibility:** spin up an identical environment from the same code.
- **Code review:** infra changes go through PRs — peers catch mistakes before they hit prod.
- **Versioning & rollback:** Git history of every infra change.
- **Drift detection:** `plan` shows when reality differs from code.
- **Documentation:** the code *is* the documentation of what exists.

---

## 4. The desired-state loop

```
your code (desired)  ─┐
                      ├─►  tool computes diff  ─►  apply changes
real infra (current) ─┘
```

This is the same control-loop idea as Kubernetes reconciliation — declare the target, let the
machine converge reality to it.

---

## 5. Where it fits

- Interviews often ask about **both Terraform and CloudFormation**, so be ready to contrast them.
- IaC pairs with **Docker** (build the artifact) and **CI/CD** (apply the infra) for full,
  repeatable delivery.
- It's the infra-side mirror of declarative application config — the same desired-state idea, applied to infrastructure.

---

## 🎤 Say this in the interview

- *"IaC makes infrastructure declarative and reviewable — I describe the desired state and the tool
  computes the diff, so environments are reproducible and changes go through PRs."*
- *"It kills configuration drift and click-ops: the code is the source of truth, versioned in Git
  with full history and rollback."*
- *"It's the same reconcile-to-desired-state loop as Kubernetes, applied to cloud resources."*

➡️ **Next:** [01 — Core workflow & state](./01_Core_Workflow_And_State.md)
