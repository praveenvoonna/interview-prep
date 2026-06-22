# 06 — The Declarative Model & Reconciliation

## 🧠 The One Idea

**Everything in Kubernetes is "you write down what you want, and a robot keeps making reality
match it."** You don't run procedures; you record a *desired state* and trust **controllers**
to continuously close the gap with *actual state*. This single idea — the **reconcile loop** —
explains Deployments, Services, the scheduler, Operators, and even OpenShift and OCM. Master
this lesson and the rest of Kubernetes becomes "the same thing, applied to a new object."

---

## 1. Desired state vs actual state

- **Desired state** = what you declared (the `spec` of your objects): "3 replicas of web."
- **Actual state** = what's really running (the `status`): "2 replicas are up."
- A **controller** watches both and **acts to make actual = desired** (start 1 more Pod).
- Then it does it again. And again. **Forever.** There is no "done" — only "currently
  reconciled."

This is why Kubernetes is **self-healing**: a crash just widens the gap, and the loop closes
it automatically. Nobody pressed a "repair" button.

---

## 2. Every object has `spec` and `status`

Open any object and you'll see the same shape:

```yaml
spec:        # DESIRED — what you (or a controller) want. You write this.
  replicas: 3
status:      # ACTUAL — what is. The controller writes this; you read it.
  replicas: 2
  availableReplicas: 2
```

- **You** set `spec`. **Controllers** report `status`. The gap between them is the controller's
  to-do list. `kubectl describe` and `kubectl get -o yaml` show both.

---

## 3. The reconcile loop (the heartbeat of K8s)

Every controller runs the same loop:

```
loop forever:
  observe  → read desired (spec) and actual (status) from the API server
  diff     → how is reality different from the wish?
  act      → create / update / delete objects to close the gap
  (then wait for the next change and repeat)
```

**Level-triggered, not edge-triggered (say this phrase):**
- **Edge-triggered** = react to the *event* ("a Pod was deleted"). If you miss the event,
  you're stuck.
- **Level-triggered** = react to the *current state* ("there should be 3, there are 2 → fix").
  Even if events are missed or arrive out of order, the controller looks at reality and
  converges. This makes Kubernetes **robust and eventually consistent**: re-running reconcile
  with the same input is safe (**idempotent**).

---

## 4. How controllers watch efficiently (the "watch" mechanism)

Controllers don't poll the API server in a tight loop (that wouldn't scale). Instead:
- They open a **watch** on the API server for a resource type and get a **stream of changes**.
- client-go caches these locally in an **informer** (a local mirror + event handlers) and a
  **work queue**, so reconciles are fast and deduplicated.
- You don't need the internals for fundamentals — just know: **controllers subscribe to
  changes via the API server and react; they don't busy-poll.** (Deep dive lives in the OCM/
  operator lessons.)

---

## 5. Declarative `apply` vs imperative commands

- **Declarative (preferred):** keep YAML in Git, `kubectl apply -f`. Kubernetes computes the
  diff and converges. This is the basis of **GitOps** (Git is the single source of desired
  state; a controller syncs the cluster to it — Argo CD/Flux).
- **Imperative:** `kubectl create`, `kubectl scale`, `kubectl delete` — direct one-off
  actions. Fine for quick fixes, but not reproducible.
- **Why declarative wins:** reproducible, reviewable (PRs), auditable, and self-documenting —
  the cluster *is* the YAML.

---

## 6. Putting it together: the Deployment example, as loops

One user action triggers a **cascade of reconcile loops**, each closing its own gap:

```
you apply Deployment(replicas=3)
   └─▶ Deployment controller: "need a ReplicaSet" → creates ReplicaSet
         └─▶ ReplicaSet controller: "need 3 Pods, have 0" → creates 3 Pods
               └─▶ Scheduler: "3 Pods have no node" → assigns nodes
                     └─▶ kubelet: "Pods assigned to me" → starts containers
                           └─▶ each reports status; loops keep watching forever
```

Kill a Pod and the **ReplicaSet loop** notices `have 2, want 3` and recreates it — no human,
no event-replay needed, just level-triggered convergence.

---

## 7. Why this matters for the role (OpenShift & OCM)

- **OpenShift** is itself built from these loops: the **Cluster Version Operator** reconciles
  the whole cluster to a desired release; dozens of operators reconcile their pieces.
- **OCM** stretches the loop **across clusters**: you declare desired state on a central hub,
  and agents on each managed cluster reconcile their cluster to match (Lesson 12).
- So "reconciliation" isn't trivia — it's the **one pattern** the entire product stack repeats
  at every level. Demonstrating you *get* it is exactly the depth the feedback was asking for.

---

## 🎤 Say this in the interview

- *"Kubernetes is declarative: I record **desired state** in `spec`, controllers report
  **actual state** in `status`, and a **reconcile loop** continuously closes the gap."*
- *"It's **level-triggered**, not edge-triggered — controllers act on current state, so
  reconciliation is **idempotent** and self-healing; missed events don't break it."*
- *"This same loop scales up: an **Operator** reconciles an app, and **OCM** reconciles whole
  clusters from a hub. It's one pattern repeated everywhere."*

➡️ **Next:** [07 — Workload controllers](./07_Workload_Controllers.md)
