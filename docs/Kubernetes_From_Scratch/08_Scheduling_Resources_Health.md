# 08 — Scheduling, Resources & Health

## 🧠 The One Idea

**Kubernetes is constantly answering two questions: "where should this Pod run?" and "is this
Pod healthy?"** The **scheduler** answers the first by matching each Pod's *needs* (CPU,
memory, constraints) to a node that can fit it. **Probes** answer the second by letting the app
prove it's alive and ready. Get the inputs right — **resource requests** and **probes** — and
the cluster places work well and self-heals; get them wrong and you get `Pending` Pods, noisy
neighbors, and traffic sent to dead apps.

---

## 1. Resources: requests vs limits (the foundation)

Every container can declare what it needs for **CPU** and **memory**:

- **request** = the **guaranteed minimum**, and the number the **scheduler uses to place** the
  Pod. "Reserve me at least this much." (CPU in millicores: `500m` = half a core; memory in
  `Mi`/`Gi`.)
- **limit** = the **hard ceiling** the container may use.
  - Exceed the **CPU** limit → it gets **throttled** (slowed), not killed.
  - Exceed the **memory** limit → it gets **`OOMKilled`** (terminated), because memory can't be
    throttled.

*Analogy: request = the seat you reserved; limit = the maximum the room will let you take
before security steps in.* Set requests so the scheduler can pack honestly; set limits to
protect neighbors from a runaway Pod.

### QoS classes (derived from requests/limits)
Kubernetes ranks Pods for **eviction under memory pressure**:
- **Guaranteed** — requests == limits for all resources (evicted last). Critical apps.
- **Burstable** — requests < limits (the common middle).
- **BestEffort** — no requests/limits set (evicted first). Avoid for anything important.

---

## 2. The scheduler: how a Pod finds a home

When a Pod has no node, the scheduler runs **two phases** (memorize these names):

1. **Filter (Predicates)** — eliminate nodes that **can't** run the Pod: not enough free
   CPU/memory (by requests), node selectors/affinity not satisfied, **taints** not tolerated,
   ports unavailable. Result: the set of *feasible* nodes.
2. **Score (Priorities)** — rank the feasible nodes and pick the **best**: spread for
   resilience (`LeastAllocated` / spread) vs pack tightly (`MostAllocated`), honor affinity
   preferences, image locality, topology spread.

Then it **binds** the Pod to the winning node (writes the assignment), and the **kubelet**
takes over and runs it. *The scheduler only decides; it never runs containers.*

> Tie-in: this Filter→Score model is exactly the bin-packing problem — match each unit of work
> to a feasible machine, then optimize. Pack-vs-spread is the core scheduling tradeoff.

---

## 3. Steering placement: nodeSelector, affinity, taints/tolerations

You can influence *where* Pods land:

- **nodeSelector** — simplest: "only nodes with label `disktype=ssd`."
- **Affinity / anti-affinity** — richer rules:
  - **node affinity** — prefer/require certain nodes (like a powerful nodeSelector).
  - **pod (anti-)affinity** — "schedule near Pods like X" (co-locate a cache) or "spread away
    from Pods like X" (don't put two replicas on the same node → resilience).
- **Taints & tolerations** — the **inverse**: a **taint** on a node *repels* Pods ("keep off
  unless you tolerate me"); a Pod with a matching **toleration** is allowed on. Used to
  **reserve** nodes (GPU nodes, control-plane nodes). *Analogy: a taint is a "staff only"
  sign; a toleration is the staff badge that lets you in.*
- **Topology spread constraints** — evenly distribute Pods across zones/nodes for HA.

---

## 4. Health checks: the three probes

The kubelet periodically checks containers so the system can self-heal and route traffic
correctly. Each probe can be an HTTP GET, a TCP connect, or an exec command.

- **livenessProbe** — "**are you alive?**" If it fails, the kubelet **restarts the container**.
  Catches deadlocks/hangs where the process is up but stuck.
- **readinessProbe** — "**are you ready for traffic?**" If it fails, the Pod is **removed from
  its Service's endpoints** (no traffic) but **not** restarted. Used during startup, warm-ups,
  or temporary overload. **This is what gates rolling updates** (Lesson 03).
- **startupProbe** — "**have you finished starting?**" For **slow-booting** apps: it disables
  liveness/readiness until the app has started, so a slow start isn't mistaken for a failure.

**Liveness vs readiness — the classic question:** *liveness restarts a broken container;
readiness just stops sending it traffic until it recovers.* Misusing liveness (e.g., failing
it on a transient dependency outage) causes needless restart loops — a common production
mistake to call out.

---

## 5. Cluster-level autoscaling (name-drop these)

- **HPA (Horizontal Pod Autoscaler)** — adds/removes **Pod replicas** based on CPU/memory or
  custom metrics. *Scale out.*
- **VPA (Vertical Pod Autoscaler)** — adjusts a Pod's **requests/limits**. *Scale up.*
- **Cluster Autoscaler** — adds/removes **nodes** when Pods can't be scheduled or nodes are
  underused. *Scale the fleet.*

---

## 6. Where this shows up when debugging

| Symptom | Likely cause |
|---|---|
| Pod stuck **Pending** | No node fits the **requests** / unsatisfied affinity / taint not tolerated |
| **OOMKilled** | Memory usage exceeded the **limit** |
| Container **restart loop** | Failing **livenessProbe** (or app crash) |
| Service has the Pod but no traffic | Failing **readinessProbe** (not in endpoints) |
| Rollout **stuck** | New Pods never become **Ready** (good — it protects you) |

---

## 🎤 Say this in the interview

- *"**Requests** are the guaranteed minimum and what the scheduler places on; **limits** are
  the ceiling — over-CPU throttles, over-memory gets **OOMKilled**. Setting both also sets the
  **QoS class** that decides eviction order."*
- *"The scheduler runs **Filter then Score** — eliminate infeasible nodes, then pick the best,
  then bind. Taints repel, tolerations permit; affinity attracts/spreads."*
- *"**Liveness** restarts a stuck container; **readiness** pulls it out of the Service until
  it's ready; **startup** protects slow boots. Readiness is what makes rolling updates safe."*

➡️ **Next:** [09 — Security: Namespaces & RBAC](./09_Security_Namespaces_RBAC.md)
