# 07 — Workload Controllers (the full family)

## 🧠 The One Idea

**There isn't one way to run Pods — there's a controller shaped for each *kind* of work.**
Deployments are for interchangeable stateless copies. But some apps need a stable identity and
their own disk (databases), some need to run on **every** node (agents), and some need to run
**once and finish** (batch jobs). Pick the controller that matches the *nature* of the work
and Kubernetes handles the rest. *Analogy: same engine (Pods), different vehicles built around
it for different journeys.*

---

## 1. The family at a glance

| Controller | Guarantee | Use it for |
|---|---|---|
| **Deployment** | N **identical, interchangeable** Pods; rolling updates | stateless web/API/workers |
| **StatefulSet** | N Pods with **stable identity + own storage**, ordered | databases, Kafka, etcd, quorum systems |
| **DaemonSet** | **exactly one Pod per node** (auto on new nodes) | log/metrics agents, CNI, node tools |
| **Job** | Run **to completion** N times, then stop | migrations, batch processing |
| **CronJob** | Run a **Job on a schedule** | backups, nightly reports |

If you only remember one rule: **stateless → Deployment; stateful → StatefulSet; per-node →
DaemonSet; finite task → Job/CronJob.**

---

## 2. StatefulSet — for apps that are "pets, not cattle"

A Deployment treats Pods as **interchangeable cattle** (random names, no personal disk). A
**StatefulSet** treats them as **named pets** — each member has a stable name, address, and its
own disk that follows it. *Analogy: a numbered apartment building — apartment 0, 1, 2 always
exist at the same address with the same furniture, even if the tenant changes.*

**The four guarantees (this is the whole topic):**
1. **Stable, ordinal identity** — Pods are named `web-0`, `web-1`, `web-2` (not random
   hashes). A replaced Pod keeps **the same name**.
2. **Stable network identity** — each Pod gets its own DNS name
   `web-0.<svc>.<ns>.svc.cluster.local`, via a **headless Service** (Lesson 05). This is how
   cluster members (Kafka/etcd) find each other to form quorum — so a StatefulSet **requires**
   a headless Service.
3. **Stable, dedicated storage** — `volumeClaimTemplates` mint **one PVC per Pod**
   (`data-web-0`, `data-web-1`…). Pod `web-2` **always** re-attaches to *its own* disk on
   restart. PVCs **survive** deletion by default (data safety).
4. **Ordered operations** — created **0 → 1 → 2** (each waits until the previous is Ready),
   deleted in reverse, updated in reverse-ordinal order.

**Knobs to mention:** `updateStrategy: RollingUpdate` with a **`partition`** for staged/canary
upgrades; `podManagementPolicy: Parallel` when ordering doesn't matter.
**Gotcha:** the StatefulSet gives identity + storage, **not** app clustering — the app still
does its own replication/leader election; K8s just guarantees names and disks.

---

## 3. DaemonSet — one Pod on every node

- Ensures **exactly one copy of a Pod runs on each node** (or each node matching a selector),
  and **automatically adds one when a new node joins**.
- Used for **node-level infrastructure**: log collectors (Fluent Bit), metrics agents (node
  exporter), the **CNI** networking plugin, storage daemons, security agents.
- *Analogy: a smoke detector in every room — add a room, you get a detector automatically.*

---

## 4. Job & CronJob — run-to-completion work

- A **Job** runs one or more Pods **until they succeed**, then stops (it doesn't keep them
  running). Good for **migrations, batch processing, one-off tasks**. Knobs: `completions`
  (how many successful runs), `parallelism` (how many at once), `backoffLimit` (retries).
  Pods use `restartPolicy: OnFailure` or `Never`.
- A **CronJob** creates a **Job on a schedule** (cron syntax: `0 2 * * *` = 2 a.m. daily).
  For **backups, reports, cleanup**. Knobs: `concurrencyPolicy` (Allow/Forbid/Replace),
  `startingDeadlineSeconds`, history limits.
- *Analogy: a Job is a task you finish and cross off; a CronJob is a recurring calendar
  reminder that creates the task for you.*

---

## 5. Choosing the right one (decision flow)

```
Does the work ever "finish"?
 ├─ Yes, on a schedule ............ CronJob
 ├─ Yes, once/finite .............. Job
 └─ No (runs continuously):
      Need one copy on every node? . DaemonSet
      Need stable identity + disk? . StatefulSet
      Otherwise (interchangeable) .. Deployment   ← the default
```

---

## 6. Tiny StatefulSet (so you've seen it)

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata: { name: db }
spec:
  serviceName: db-headless        # REQUIRED: the headless Service for per-Pod DNS
  replicas: 3
  selector: { matchLabels: { app: db } }
  template:
    metadata: { labels: { app: db } }
    spec:
      containers:
        - name: db
          image: postgres:16
          volumeMounts: [{ name: data, mountPath: /var/lib/postgresql/data }]
  volumeClaimTemplates:           # one PVC PER Pod (data-db-0, data-db-1, ...)
    - metadata: { name: data }
      spec:
        accessModes: ["ReadWriteOnce"]
        resources: { requests: { storage: 10Gi } }
```

---

## 🎤 Say this in the interview

- *"Deployment for stateless, interchangeable replicas; **StatefulSet** when each Pod needs a
  stable name, stable DNS (headless Service), and its own PVC — databases and quorum systems."*
- *"**DaemonSet** = one Pod per node, for agents/CNI; **Job/CronJob** = run-to-completion,
  one-off or scheduled."*
- *"A StatefulSet gives identity and storage, not clustering — the app still handles
  replication and leader election itself."*

➡️ **Next:** [08 — Scheduling, resources & health](./08_Scheduling_Resources_Health.md)
