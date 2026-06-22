# 10 — Extending Kubernetes: CRDs & Operators

## 🧠 The One Idea

**Kubernetes lets you teach it new nouns and hire a robot to manage them.** A **CRD** adds a
new *kind* of object to the API (a new noun, e.g. `PostgresCluster`). A **custom controller**
is the robot that watches those objects and makes them real. Put the two together and you have
an **Operator**: *an expert SRE's runbook, encoded as software, that runs the reconcile loop
for a specific app forever.* This is the pattern OpenShift and OCM are built on — so this
lesson is the bridge to the role.

---

## 1. Why extend Kubernetes at all?

Built-in objects (Pod, Deployment, Service) are generic. But real apps need **app-specific
operational knowledge**: how to back up a database, add a replica to a cluster, do a safe
version upgrade, recover from failure. Kubernetes lets you **package that expertise** as a
first-class API object + controller — so operating the app becomes as declarative as running a
Pod. You go from *"here are 12 manual steps"* to *"I declared `replicas: 5`, the Operator did
the steps."*

---

## 2. CRD — Custom Resource Definition (the new noun)

- A **CRD** registers a **new resource type** with the API server. After you apply it, the
  cluster understands a brand-new kind — say `kind: PostgresCluster` — and you can
  `kubectl get postgresclusters` just like built-ins.
- The objects you then create of that type are **Custom Resources (CRs)**. They get stored in
  etcd and served by the API server **exactly like native objects** — same YAML, same RBAC,
  same `kubectl`, same `spec`/`status`.
- A CRD defines the **schema** (fields + validation via OpenAPI). *Analogy: a CRD is adding a
  new word to the dictionary; a CR is a sentence using that word.*

```yaml
# A custom resource (the "what I want"), once its CRD is installed:
apiVersion: acme.example.com/v1
kind: PostgresCluster
metadata: { name: orders-db }
spec:
  replicas: 3          # the Operator knows how to make a 3-node Postgres real
  version: "16"
```

> **Key point:** a CRD by itself is just **data** — it stores the object but *does nothing*.
> The behavior comes from a controller watching it. CRD = the noun; controller = the verb.

---

## 3. Custom controller — the robot that acts

- A **controller** for your CR runs the same **reconcile loop** as every built-in controller
  (Lesson 06): watch `PostgresCluster` objects → compare desired (`spec`) to actual → create
  the StatefulSets, Services, Secrets, PVCs, run backups, perform upgrades → update `status`.
- It's **level-triggered and idempotent**: re-running reconcile converges to the same result,
  so it's robust to restarts and missed events.
- In Go, you typically build it with **controller-runtime / Operator SDK / Kubebuilder**, which
  give you the watch/informer/work-queue plumbing so you just write the `Reconcile()` function.

---

## 4. Operator = CRD + custom controller

**Operator = a CRD (new API) + a controller that encodes operational know-how for a specific
app.** The famous definition: *"an Operator is a way to package, deploy, and manage a
Kubernetes application — it puts an SRE's runbook into code."*

- **Without an Operator:** to run Postgres you hand-craft StatefulSets, Services, backup
  CronJobs, and you personally perform upgrades/failover.
- **With an Operator:** you write `kind: PostgresCluster, spec.replicas: 3, version: 16`. The
  Operator continuously creates and maintains all those pieces, handles failover, runs backups,
  and does safe upgrades — **automatically, forever**.

**Examples:** the Prometheus Operator, cert-manager, database operators (Postgres, etcd) — and,
crucially, **OpenShift itself is a giant collection of Operators**, and **OCM** ships its
features as Operators too.

---

## 5. How it all fits the reconcile model

```
You apply a CR (PostgresCluster, replicas=3)   ← desired state, a new "noun"
        │  watched by
   Custom controller (the Operator)             ← the reconcile loop / "verb"
        │  creates & maintains
   StatefulSet + Services + Secrets + backups   ← built-in objects doing the work
        │  reports
   status back onto the CR                       ← actual state
```

It's the **exact same pattern** as a Deployment managing Pods — just one level up, applied to
*your* domain object. **That's the whole magic: Kubernetes is extensible by reusing its own
control-loop machinery.**

---

## 6. Related extension points (name-drop for depth)
- **Admission webhooks** — intercept create/update requests to **validate** (reject bad
  objects) or **mutate** (inject defaults/sidecars) them. This is how policy engines
  (Gatekeeper/OPA) and service meshes hook in.
- **Aggregated API servers** — for advanced cases, serve a custom API alongside the core one
  (rarely needed; CRDs cover most needs).
- **OLM (Operator Lifecycle Manager)** — on OpenShift, installs/updates Operators and their
  CRDs cleanly (Lesson 11).

---

## 🎤 Say this in the interview

- *"A **CRD** adds a new resource type to the API; a **CR** is an instance of it, stored and
  served like a native object — but a CRD alone is just data."*
- *"An **Operator** = CRD + a custom controller that runs the reconcile loop, encoding an SRE
  runbook in code: it watches my custom resource and continuously builds and maintains the real
  resources to match."*
- *"It's the same control-loop pattern as a Deployment, one level up — and it's exactly how
  OpenShift and OCM are built, which is why I'm comfortable in that codebase."*

➡️ **Next:** [11 — OpenShift on top of Kubernetes](./11_OpenShift_On_Top.md)
