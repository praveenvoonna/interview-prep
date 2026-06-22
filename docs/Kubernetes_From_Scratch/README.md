# 🎓 Kubernetes From Scratch — A 1-Day Crash Course (in plain English)

Need to get your **Kubernetes fundamentals solid** in **one day**? This folder teaches
Kubernetes **from zero, in naive terms**, building one concept on top of the last — then
bridges to **OpenShift** and **OCM (Open Cluster Management)**.

> **How to read this:** go in order, 00 → 14. Each lesson starts with a **plain-English
> analogy ("The One Idea")**, then the real details, then **"Say this in the interview"**
> one-liners you can repeat almost verbatim. Don't skip the analogies — they're the hooks
> that make the rest stick.

---

## 📂 The lessons (read in order)

### Part A — The mental model (don't skip)
- **[00 — What is Kubernetes & why it exists](./00_What_Is_Kubernetes.md)** — containers,
  the problem K8s solves, "desired state" in one picture.
- **[01 — Architecture big picture](./01_Architecture_Big_Picture.md)** — control plane vs
  worker nodes, the components, and the **reconcile loop** that runs everything.

### Part B — The core objects (the 90% you use daily)
- **[02 — Pods, the atom](./02_Pods_The_Atom.md)** — the smallest unit; lifecycle; multi-
  container patterns.
- **[03 — Controllers & Deployments](./03_Controllers_And_Deployments.md)** — ReplicaSets,
  Deployments, rolling updates, self-healing.
- **[04 — Config & Storage](./04_Config_And_Storage.md)** — ConfigMaps, Secrets, Volumes,
  PV/PVC/StorageClass.
- **[05 — Networking: Services & DNS](./05_Networking_Services_And_DNS.md)** — Service types,
  kube-proxy, cluster DNS, Ingress.

### Part C — How it really works + the rest of the objects
- **[06 — The declarative model & reconciliation](./06_Declarative_Model_And_Reconciliation.md)**
  — desired vs actual, etcd, controllers, level-triggered logic.
- **[07 — Workload controllers](./07_Workload_Controllers.md)** — StatefulSet, DaemonSet,
  Job, CronJob — when to use each.
- **[08 — Scheduling, resources & health](./08_Scheduling_Resources_Health.md)** —
  requests/limits, QoS, probes, the scheduler, taints/affinity.
- **[09 — Security: Namespaces & RBAC](./09_Security_Namespaces_RBAC.md)** — isolation,
  Roles/Bindings, ServiceAccounts, least privilege.

### Part D — Extending K8s + the products this role uses
- **[10 — Extending Kubernetes: CRDs & Operators](./10_Extending_K8s_CRDs_Operators.md)** —
  custom resources, controllers, the operator pattern.
- **[11 — OpenShift on top of Kubernetes](./11_OpenShift_On_Top.md)** — what OpenShift adds
  (Routes, SCC, OLM, CVO, Projects, `oc`).
- **[12 — OCM: multicluster from scratch](./12_OCM_Multicluster_From_Scratch.md)** — hub-
  spoke, ManagedCluster, ManifestWork, Placement, addons.

### Part E — Practice
- **[13 — Hands-on lab](./13_Hands_On_Lab.md)** — spin up a real cluster with `kind` and run
  the commands so the concepts become muscle memory.
- **[14 — Interview Q&A flashcards](./14_Interview_QA_Flashcards.md)** — rapid-fire
  questions with crisp answers across every topic.

---

## 🗓️ Suggested 1-day plan

| Time | Do this |
|---|---|
| **Morning (3 hrs)** | Lessons **00–05**. These are the fundamentals the feedback is about. |
| **Midday (1 hr)** | Lesson **13 hands-on** — create a kind cluster, deploy nginx, break it, watch it heal. *Doing beats reading.* |
| **Afternoon (3 hrs)** | Lessons **06–10** — how it really works + CRDs/Operators. |
| **Late afternoon (1.5 hrs)** | Lessons **11–12** — OpenShift + OCM (the role's actual products). |
| **Evening (1 hr)** | Lesson **14 flashcards** — read aloud, twice. |

**If you only have 90 minutes:** read **00, 01, 03, 05, 10** and the **14 flashcards**.
Those five carry the most interview weight.

---

## 🧠 The whole course in three sentences

1. **Kubernetes is a control loop:** you declare the *desired state* (YAML), and controllers
   continuously make the *actual state* match it — that's the entire philosophy.
2. **Everything is an object in etcd** (Pods, Services, Deployments…), wired together by
   **labels and selectors**, and reconciled by **controllers** watching the API server.
3. **OpenShift** is Kubernetes + enterprise batteries; **OCM** applies that same control-loop
   idea **across many clusters** from a central hub.

If you can say those three sentences and explain each, you've got the backbone.

---

## 🛠️ Tools to install for the lab (Lesson 13)
- **`kubectl`** — the CLI you talk to the cluster with.
- **`kind`** (Kubernetes-in-Docker) or **`minikube`** — a throwaway local cluster.
- **Docker / Podman** — the container runtime kind/minikube sit on.
- (Optional) **`oc`** — the OpenShift CLI; **`clusteradm`** — the OCM CLI.

You've got this. Start at **[00](./00_What_Is_Kubernetes.md)**.
