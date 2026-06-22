# 11 — OpenShift on Top of Kubernetes

## 🧠 The One Idea

**OpenShift is Kubernetes with the batteries included and the safety rails bolted on.** Plain
Kubernetes is an *engine*; OpenShift is the *whole car* — same engine, plus security defaults,
a built-in router and registry, developer tooling, an install/upgrade system, and a web
console. Everything you learned in lessons 00–10 **still applies** — OpenShift is a certified
Kubernetes distribution. This lesson is just **"what Red Hat adds on top,"** which is exactly
what this role works on.

---

## 1. The one-sentence positioning

> **OpenShift = upstream Kubernetes + enterprise security, an integrated developer/operations
> platform, and a fully *operator-driven* install & upgrade story.**

If asked "OpenShift vs Kubernetes," say: *"Same Kubernetes core and APIs; OpenShift adds
opinionated security (SCC, non-root by default), Routes, an internal registry, OLM for
operators, the Cluster Version Operator for one-click upgrades, build/CI tooling, and a
console — so it's a supported, batteries-included platform."*

---

## 2. The big additions (know these names cold)

### Projects (= Namespaces, plus)
- An OpenShift **Project** is a **Namespace with extra metadata and self-service** (users can
  request their own projects, with default quotas/roles applied). Conceptually: namespace +
  multi-tenancy policy.

### Routes (= the original Ingress)
- A **Route** exposes a Service at a public hostname via OpenShift's built-in **HAProxy
  router**. It predates Ingress and is simpler for TLS: **edge** (TLS terminated at the
  router), **passthrough** (TLS straight to the Pod), **re-encrypt** (terminate then re-encrypt
  to the Pod). Routes and standard Ingress both work on OpenShift.

### SCC — Security Context Constraints (the security headline)
- **SCC is to Pods what RBAC is to API actions** — it controls **what a Pod is allowed to do at
  runtime** (run as root? use host network? which UID range? which volumes/capabilities?).
- OpenShift's default (`restricted-v2`) makes Pods **run as non-root with a random UID** — much
  stricter than vanilla Kubernetes. *This is the #1 thing that "just works on K8s" but needs
  attention on OpenShift* (images assuming root or fixed UIDs fail until you grant an SCC or fix
  the image). Great to mention as real-world awareness.

### OLM — Operator Lifecycle Manager
- The system that **installs, versions, and upgrades Operators** (and their CRDs) cleanly, from
  catalogs (OperatorHub). It's how the operator pattern from Lesson 10 is **managed** as a
  product feature, with channels and automatic updates.

### CVO — Cluster Version Operator (the self-driving part)
- OpenShift is installed and upgraded as **one versioned release payload**. The **CVO**
  reconciles the *entire cluster* to that desired version, driving ~30+ second-level operators
  that each manage one component (networking, console, auth…). A cluster upgrade is **one
  declarative action** — `oc adm upgrade` — and the CVO does the rest. *It's "Tesla OTA
  firmware" for a Kubernetes cluster — the reconcile loop applied to the whole platform.*

### Internal image registry
- A built-in container **registry** so you can build and store images in-cluster without an
  external one.

### Builds & CI/CD
- **BuildConfig / Source-to-Image (S2I)** — build a container image straight from source code
  (no Dockerfile needed). Plus **OpenShift Pipelines (Tekton)** for cloud-native CI/CD and
  **OpenShift GitOps (Argo CD)** for declarative delivery.

### Networking & more
- Default CNI is **OVN-Kubernetes**; integrated **monitoring** (Prometheus/Thanos/Grafana),
  **logging**, **OAuth** identity integration, and a full **web console**.

---

## 3. `oc` — the OpenShift CLI

- **`oc` is `kubectl` plus OpenShift extras.** Everything `kubectl` does, `oc` does (it's a
  superset), and it adds OpenShift-aware commands:
  - `oc new-project`, `oc project` — work with Projects.
  - `oc new-app` — create an app (with S2I builds) in one command.
  - `oc expose svc/...` — create a Route.
  - `oc adm ...` — admin tasks (e.g., `oc adm upgrade`, manage SCCs).
  - `oc login` — auth via OpenShift's OAuth.

---

## 4. How it all maps back to what you learned

| Plain Kubernetes | OpenShift equivalent / addition |
|---|---|
| Namespace | **Project** (self-service + policy) |
| Ingress | **Route** (HAProxy, easy TLS modes) — both supported |
| PodSecurity (admission) | **SCC** (stricter, non-root by default) |
| Install operators manually | **OLM** + OperatorHub |
| Upgrade components yourself | **CVO** — one versioned release, self-applying |
| Bring your own registry/CI | **Built-in registry, S2I builds, Tekton, Argo CD** |
| `kubectl` | **`oc`** (superset) |

**Everything else — Pods, Deployments, Services, RBAC, CRDs, Operators — is identical.** That's
the reassuring message: your fundamentals transfer 1:1; OpenShift just adds a managed, secure
shell around them.

---

## 🎤 Say this in the interview

- *"OpenShift is a certified Kubernetes distribution — same APIs — plus enterprise pieces:
  **SCC** (non-root by default), **Routes**, an internal registry, **OLM** for operators, the
  **CVO** for one-click cluster upgrades, S2I builds, Tekton/Argo, and a console."*
- *"The thing that bites people moving from K8s to OpenShift is **SCC** — the default
  `restricted-v2` forces non-root with a random UID, so images that assume root need fixing or
  a granted SCC."*
- *"`oc` is a superset of `kubectl`; a cluster upgrade is a single declarative action that the
  **Cluster Version Operator** reconciles across all the platform operators."*

➡️ **Next:** [12 — OCM: multicluster from scratch](./12_OCM_Multicluster_From_Scratch.md)
