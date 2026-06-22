# 14 — Interview Q&A Flashcards

Read these **out loud**. Cover the answer, ask yourself the question, then check. Two passes
the night before is worth more than re-reading the lessons. Grouped by topic.

---

## A. Fundamentals & philosophy
- **Q: What is Kubernetes in one sentence?**
  A: A declarative container orchestrator — I describe desired state and a **control loop**
  continuously reconciles actual state to match it (a thermostat for apps).
- **Q: Imperative vs declarative?**
  A: Imperative = step-by-step commands; declarative = describe the end state and let
  controllers converge. Declarative is reproducible, reviewable (GitOps), and self-healing.
- **Q: What does "level-triggered" mean and why does it matter?**
  A: Controllers act on **current state**, not on events. So reconciliation is idempotent and
  robust — missed/duplicate events don't break it; it always converges.
- **Q: spec vs status?**
  A: `spec` = desired (I write it); `status` = actual (controller writes it). The gap is the
  controller's to-do list.

## B. Architecture
- **Q: Control-plane components?**
  A: **API server** (only entry point), **etcd** (state store), **scheduler** (placement),
  **controller-manager** (reconcile loops), cloud-controller-manager.
- **Q: Worker-node components?**
  A: **kubelet** (runs/report Pods), **container runtime** via CRI (containerd/CRI-O),
  **kube-proxy** (Service networking), **CNI plugin** (Pod IPs/connectivity).
- **Q: What happens on `kubectl apply -f deploy.yaml`?**
  A: API server authn/authz/validates → writes to etcd → Deployment ctrl makes a ReplicaSet →
  RS ctrl makes Pods → scheduler binds nodes → kubelet starts containers → status reported. All
  the same watch-and-reconcile pattern.
- **Q: What is etcd and why protect it?**
  A: The distributed key-value store holding all cluster state — the single source of truth;
  back it up because losing it loses the cluster's brain.

## C. Pods & controllers
- **Q: What is a Pod?**
  A: Smallest deployable unit — one or more containers sharing an IP/network/storage,
  co-scheduled, live/die together. Disposable: replaced, never repaired.
- **Q: Deployment vs ReplicaSet?**
  A: ReplicaSet keeps N identical Pods; Deployment manages ReplicaSets and adds rolling
  updates + rollback. I use Deployments.
- **Q: How does a rolling update work?**
  A: New ReplicaSet scaled up while old scales down, gated by **readiness probes** and bounded
  by **maxSurge/maxUnavailable**; old RS kept for instant rollback.
- **Q: init container vs sidecar?**
  A: init runs to completion **before** the app (setup); sidecar runs **alongside** for the
  Pod's life (logging, mesh proxy).
- **Q: Deployment vs StatefulSet vs DaemonSet vs Job?**
  A: Stateless replicas / stable-identity+storage (DBs, headless Service, per-Pod PVC) /
  one-per-node agents / run-to-completion (CronJob = scheduled Job).

## D. Networking
- **Q: Why Services?**
  A: Pods are disposable and change IPs; a Service gives a stable VIP + DNS name that
  load-balances to the **Ready** Pods matching its selector.
- **Q: Service types?**
  A: ClusterIP (internal), NodePort (node port), LoadBalancer (cloud LB), ExternalName (CNAME),
  Headless (per-Pod DNS, for StatefulSets).
- **Q: How does kube-proxy work?**
  A: Programs iptables/IPVS (or eBPF) on each node so traffic to the ClusterIP is rewritten to
  a backend Pod IP — kernel-level load balancing, no central bottleneck.
- **Q: Ingress vs Service?**
  A: Service is L4 (TCP/UDP) to Pods; Ingress/Route is an L7 HTTP router (host/path) + TLS in
  front of many Services. Ingress needs an Ingress controller to do anything.
- **Q: How does service discovery work?**
  A: CoreDNS gives every Service a name: `svc.namespace.svc.cluster.local`. Apps connect by
  name, never IP.

## E. Config, storage, scheduling, health
- **Q: ConfigMap vs Secret?**
  A: Both inject config (env or files); Secret is for sensitive data but only **base64-encoded**
  — real safety = etcd encryption-at-rest + RBAC (or Vault/ESO).
- **Q: PV vs PVC vs StorageClass?**
  A: PV = the supply (a real disk); PVC = the demand (a request); StorageClass = dynamic
  provisioner that auto-creates a PV for a PVC. Access modes RWO/ROX/RWX.
- **Q: requests vs limits?**
  A: request = guaranteed minimum + what the scheduler places on; limit = hard cap. Over-CPU →
  throttled; over-memory → **OOMKilled**. They set the **QoS class** (eviction order).
- **Q: How does the scheduler work?**
  A: **Filter** (drop infeasible nodes) → **Score** (rank, pack vs spread) → **bind**. Taints
  repel, tolerations permit; affinity attracts/spreads.
- **Q: Liveness vs readiness vs startup probe?**
  A: Liveness restarts a stuck container; readiness pulls it out of the Service until ready
  (gates rollouts); startup protects slow boots from premature liveness failures.

## F. Security
- **Q: Namespaced vs cluster-scoped?**
  A: Pods/Services/Deployments/Roles are namespaced; Nodes/PVs/Namespaces/StorageClasses/
  ClusterRoles/CRDs are cluster-scoped.
- **Q: Explain RBAC.**
  A: Role/ClusterRole = the permissions (verbs on resources); RoleBinding/ClusterRoleBinding =
  who gets them. **Additive, no deny** → design for least privilege; verify with
  `kubectl auth can-i`.
- **Q: What's a ServiceAccount?**
  A: An identity for a Pod/workload to call the API; give each workload its own with minimal
  rights instead of reusing `default`.

## G. Extensibility, OpenShift, OCM
- **Q: What's a CRD? An Operator?**
  A: CRD adds a new API type (a noun); Operator = CRD + a custom controller that reconciles it
  — an SRE runbook in code. Same control loop, one level up.
- **Q: OpenShift vs Kubernetes?**
  A: Same K8s core + enterprise: **SCC** (non-root by default), **Routes**, internal registry,
  **OLM** (operators), **CVO** (one-click upgrades), S2I/Tekton/Argo, console; `oc` ⊃ `kubectl`.
- **Q: What bites you moving K8s → OpenShift?**
  A: **SCC** — `restricted-v2` forces non-root random UID, so root-assuming images fail until
  fixed or granted an SCC.
- **Q: What is OCM and the pull model?**
  A: Hub-spoke multicluster management; spokes run a **klusterlet** that **pulls** desired state
  from the hub — more secure (no inbound access) and resilient. Core APIs: **ManagedCluster**,
  **Placement** (cluster selector), **ManifestWork** (resource bundle to a spoke).
- **Q: How does OCM relate to core Kubernetes?**
  A: Same patterns one level up — CRDs/controllers, labels→Placement, reconcile→hub/spoke pull.
  "Kubernetes for your clusters."

---

## 🎯 The 3-sentence backbone (if you blank, say this)
1. *"Kubernetes is a declarative control loop — I declare desired state and controllers
   reconcile actual to match it, forever."*
2. *"Everything is an object in etcd, wired by labels/selectors, reconciled by controllers
   watching the API server."*
3. *"OpenShift adds enterprise batteries on the same core; OCM applies the same loop across
   many clusters from a hub — so my fundamentals transfer directly."*

Good luck — go get it. 🚀
