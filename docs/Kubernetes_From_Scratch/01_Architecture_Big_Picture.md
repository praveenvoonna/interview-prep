# 01 — Architecture Big Picture

## 🧠 The One Idea

**A Kubernetes cluster is a company with a head office and factories.** The **control plane**
is the head office: it makes all the decisions, keeps the records, and tells the factories
what to do. The **worker nodes** are the factories: they actually run your containers. You
(the user) only ever talk to the **front desk of the head office — the API server** — and
everyone else talks through it too. Nobody talks to anybody directly; **everything goes
through the API server.**

A **cluster** = control plane (the brain) + one or more worker nodes (the muscle).

---

## 1. The two halves

```
        YOU (kubectl) ──▶ ┌──────────── CONTROL PLANE (the brain) ───────────┐
                          │  API server  │  etcd  │  scheduler  │ controller- │
                          │  (front desk)│ (memory)│ (placer)   │  manager    │
                          └───────┬───────────────────────────────┬──────────┘
                                  │  (all communication via API)   │
                ┌─────────────────┴──────────┐      ┌──────────────┴───────────┐
                │  WORKER NODE 1              │      │  WORKER NODE 2            │
                │  kubelet │ kube-proxy │ CRI │      │  kubelet │ kube-proxy│CRI │
                │   └─ Pods (your containers) │      │   └─ Pods               │
                └────────────────────────────┘      └──────────────────────────┘
```

---

## 2. Control plane components (the head office)

These run the cluster. (In production they're replicated for HA; in OpenShift they live on
dedicated **master/control-plane nodes**.)

- **API server (`kube-apiserver`) — the front desk / receptionist.** The **only** way in and
  out. Every command, every component, every controller talks to it. It validates requests,
  handles auth, and is the single door to the database. *If you remember one component,
  remember this one.*
- **etcd — the company's memory / filing cabinet.** A distributed **key-value database** that
  stores the **entire cluster state** (every object, desired and actual). It's the single
  source of truth. Lose etcd = lose the cluster's brain (so you back it up). Only the API
  server talks to etcd directly.
- **scheduler (`kube-scheduler`) — the seating planner.** When a new Pod needs a home, it
  decides **which node** runs it, based on resources, constraints, affinity, taints. It only
  *decides*; the kubelet does the running. (See Lesson 08.)
- **controller-manager — the team of robot managers.** Runs all the built-in **controllers**
  (the reconcile loops): the Deployment controller, ReplicaSet controller, Node controller,
  Job controller, etc. Each watches the API server and works to make actual = desired.
- **cloud-controller-manager** (on cloud) — talks to the cloud provider for load balancers,
  disks, and node lifecycle.

---

## 3. Worker node components (the factory floor)

Every node that runs your workloads has:

- **kubelet — the factory foreman.** The agent on each node. It watches the API server for
  "Pods assigned to me," then tells the container runtime to start them, and **reports their
  health back** to the API server. It's the bridge between the brain and the containers.
- **container runtime (CRI) — the machinery.** The software that actually runs containers —
  **containerd** or **CRI-O** (OpenShift uses CRI-O). Kubernetes talks to it via the
  **Container Runtime Interface (CRI)**, so it's pluggable. (Docker itself was dropped as a
  runtime in 1.24.)
- **kube-proxy — the receptionist's switchboard.** Implements **Service** networking on each
  node: it programs iptables/IPVS (or eBPF) rules so traffic to a Service's virtual IP gets
  load-balanced to the right Pods. (See Lesson 05.)
- **CNI plugin — the wiring.** Gives each Pod its IP and connects Pods across nodes
  (Calico/Cilium/OVN-Kubernetes). Kubernetes defines the *contract*; the plugin implements it.

---

## 4. How a request actually flows (the classic interview question)

**"What happens when you run `kubectl apply -f deployment.yaml`?"** Walk it like this:

1. **kubectl → API server.** Your YAML is authenticated, authorized (RBAC), validated, and
   the **Deployment object is written to etcd**. Your command returns "created." *Nothing is
   running yet — you've only recorded a wish.*
2. **Deployment controller** (in controller-manager) is *watching*. It sees a new Deployment
   wanting 3 replicas, so it creates a **ReplicaSet**.
3. **ReplicaSet controller** sees a ReplicaSet wanting 3 Pods but 0 exist → it creates **3 Pod
   objects** in etcd (still just records, `Pending`, no node assigned).
4. **Scheduler** is watching for Pods with no node. It picks a node for each Pod and writes
   that assignment back to the API server.
5. **kubelet** on each chosen node sees "a Pod is assigned to me," pulls the image, and tells
   **CRI-O/containerd** to start the containers.
6. **kubelet reports status** ("Running") back to the API server → etcd. `kubectl get pods`
   now shows them Running.

**The punchline:** every step is the same pattern — a component **watches** the API server,
notices a gap between desired and actual, and **acts to close it**. That's reconciliation.

---

## 5. The mental shortcut

- **You** only ever talk to the **API server**.
- **etcd** remembers everything.
- **Controllers** watch and reconcile (make actual = desired).
- **Scheduler** decides *where*.
- **kubelet** makes it *happen* on the node.
- **kube-proxy + CNI** make the networking work.

---

## 🎤 Say this in the interview

- *"The control plane is the brain — API server (the only entry point), etcd (the state
  store), scheduler (placement), and controllers (reconcile loops). Worker nodes run the
  kubelet, a CRI runtime, and kube-proxy to actually run Pods."*
- *"Everything goes through the **API server**, and state lives in **etcd**. Components don't
  talk to each other directly — they **watch the API server** and reconcile."*
- For the flow question, walk the **6 steps above** — it shows you understand the system, not
  just the vocabulary.

➡️ **Next:** [02 — Pods, the atom](./02_Pods_The_Atom.md)
