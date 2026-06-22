# 12 — OCM: Multicluster From Scratch

## 🧠 The One Idea

**Once you have many Kubernetes clusters, you need a Kubernetes *for your clusters* — and
that's OCM.** Open Cluster Management applies the **exact same control-loop idea** you've
learned, but one level up: instead of a control plane managing Pods on nodes, a central **hub
cluster** manages **whole clusters** (the "spokes"). You declare *"run this app on all
production clusters in Europe,"* and OCM reconciles that across the fleet. *Analogy: a single
cluster is a factory; OCM is the head office coordinating dozens of factories worldwide.*

> **OCM** is the open-source upstream; **Red Hat Advanced Cluster Management (RHACM)** is the
> productized version on OpenShift. This role works on the **open-source OCM**.

---

## 1. Why multicluster? (the problem)

One cluster isn't enough in the real world: you have clusters per **region** (latency, data
residency), per **environment** (dev/stage/prod), per **cloud**, plus **edge** clusters. Now:
- How do you **deploy the same app** to 200 clusters consistently?
- How do you **enforce policy/compliance** everywhere at once?
- How do you get **one view** of fleet health, and target *the right* clusters?

Doing it cluster-by-cluster doesn't scale. OCM gives you **one declarative control point** for
the whole fleet — and crucially does it **without the hub needing constant low-level access**
into each cluster.

---

## 2. Hub & Spoke architecture (the core)

```
        ┌──────────────── HUB cluster (the head office) ───────────────┐
        │  ManagedCluster registry │ Placement │ ManifestWork │ Policy  │
        └───────────┬───────────────────────────────┬──────────────────┘
            register & PULL desired state    register & PULL desired state
        ┌───────────┴──────────┐          ┌─────────┴───────────┐
        │  Spoke cluster A      │          │  Spoke cluster B     │
        │  (klusterlet agent)   │          │  (klusterlet agent)  │
        └───────────────────────┘          └──────────────────────┘
```

- **Hub** — the central cluster where you declare intent and see fleet state.
- **Spokes (managed clusters)** — the clusters being managed. Each runs an agent called the
  **klusterlet** that **pulls** work from the hub and applies it locally.

**The pull model (say this — it's the key design choice):** spokes **pull** their desired state
from the hub rather than the hub **pushing** into them. Why it's better at scale:
- **Security** — the hub needs no inbound credentials/network path into each spoke; the spoke
  initiates the connection outward. Smaller attack surface.
- **Scale & resilience** — works across NAT/firewalls/edge, and a spoke keeps running its last
  state even if the hub is temporarily unreachable. It's the **reconcile loop, federated**.

---

## 3. The core OCM APIs (the vocabulary to know)

- **ManagedCluster** — a hub object representing a registered spoke (its identity, status,
  labels like `region=eu`, `env=prod`). Registration is a secure two-way handshake (the spoke's
  klusterlet joins; the hub approves).
- **ManifestWork** — *"here is a bundle of Kubernetes resources I want applied to a specific
  spoke."* The hub creates a ManifestWork targeted at a cluster; that spoke's klusterlet pulls
  it and applies the manifests, then **reports status back**. This is the fundamental
  **hub→spoke delivery primitive**.
- **Placement** — *"which clusters should this apply to?"* A Placement **selects a set of
  ManagedClusters** by labels/claims/predicates (e.g., "all `env=prod` in `region=eu`"). It
  outputs a **PlacementDecision** (the chosen clusters). This is the fleet-level analogue of a
  **label selector** — instead of selecting Pods, it selects clusters.
- **ManagedClusterSet** — a grouping of clusters for organization and RBAC boundaries
  (bind sets to namespaces so teams only target their own clusters).
- **Add-ons** — pluggable capabilities deployed to spokes via the addon framework (policy,
  observability, application delivery). Each is essentially an operator extending OCM.

---

## 4. How a fleet deployment flows (tie it together)

```
1. Spokes register → ManagedCluster objects appear on the hub (labelled by region/env).
2. You write a Placement: "select clusters where env=prod, region=eu."
   → OCM produces a PlacementDecision listing the matching clusters.
3. Something (e.g., an ApplicationSet / policy controller) reads that decision and creates
   a ManifestWork per selected cluster with the app's resources.
4. Each target spoke's klusterlet PULLS its ManifestWork and applies it locally,
   then reports status back to the hub.
5. The hub aggregates status → you see, in one place, the app rolled out across the fleet.
```

It's **Placement (which clusters) + ManifestWork (what to apply) + klusterlet (pull & apply) +
status back** — the same observe/diff/act loop, stretched across clusters.

---

## 5. What gets built on this base (RHACM pillars)
- **Application lifecycle / GitOps** — deliver apps to selected clusters (often Argo CD
  **ApplicationSet** driven by Placement decisions).
- **Governance, Risk & Compliance (Policy)** — define a **Policy** once on the hub; it's
  distributed to selected spokes in **inform** (report violations) or **enforce** (auto-
  remediate) mode — fleet-wide compliance as reconcile loops.
- **Observability** — aggregate metrics from all spokes to the hub (Thanos) for a single
  fleet view.
- **Cluster lifecycle** — create/import/upgrade/detach whole clusters from the hub.

---

## 6. Why your fundamentals carry over
Everything from lessons 00–10 reappears here, one level up:
- **CRDs** define ManagedCluster/Placement/ManifestWork; **controllers** reconcile them
  (Lesson 10).
- **Labels & selectors** become **Placement** selecting clusters (Lessons 03/05).
- **Reconcile loop** becomes **hub↔spoke pull-and-apply** (Lesson 06).
- **RBAC + ServiceAccounts** secure registration and per-team cluster sets (Lesson 09).

*"OCM is Kubernetes patterns applied to clusters instead of Pods"* — if you can say that and
back it with the APIs above, you've shown exactly the depth this role wants.

---

## 🎤 Say this in the interview

- *"OCM is hub-and-spoke multicluster management: a **hub** holds desired state, each spoke runs
  a **klusterlet** that **pulls** and applies it. The pull model is more secure and scalable —
  no inbound access into spokes, resilient to hub outages."*
- *"Core APIs: **ManagedCluster** (a registered spoke), **Placement** (selects clusters by
  labels — a cluster-level selector), and **ManifestWork** (a bundle of resources delivered to
  a spoke). RHACM's app/policy/observability features build on these."*
- *"It's the same reconcile loop as core Kubernetes, just federated across clusters — which is
  why my Kubernetes fundamentals transfer directly."*

➡️ **Next:** [13 — Hands-on lab](./13_Hands_On_Lab.md)
