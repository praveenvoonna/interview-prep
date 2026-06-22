# 03 — Controllers & Deployments

## 🧠 The One Idea

**A controller is a robot manager that watches one number and never stops fixing it.** You say
"I want 3 copies." The controller constantly counts: *3? good. 2? start one. 4? kill one.*
A **Deployment** is the controller you'll use 90% of the time — it manages stateless app
copies and gives you **zero-downtime rollouts and instant rollbacks** for free. *You manage
the Deployment; the Deployment manages the Pods; you never touch Pods directly.*

---

## 1. Why not just create Pods?

Bare Pods don't self-heal — delete one, or lose its node, and it's gone forever. You want a
**controller** sitting above the Pods whose entire job is "**keep the right Pods alive**."
That controller runs the reconcile loop from Lesson 00: observe actual count → compare to
desired → create/delete to close the gap → repeat.

The chain of command for a stateless web app:

```
Deployment  ──manages──▶  ReplicaSet  ──manages──▶  Pods  ──run──▶  containers
(rollouts)               (keeps N alive)          (your app)
```

---

## 2. ReplicaSet — "keep exactly N copies alive"

- A **ReplicaSet's** only job: ensure **exactly N identical Pods** exist, matching a label
  selector. Too few → create more; too many → delete some.
- It finds "its" Pods by **label selector** (e.g. `app=web`). *Labels are the wiring.*
- **You rarely create a ReplicaSet directly** — a Deployment creates and manages it for you.

That's it. ReplicaSet = the headcount enforcer. The interesting part (updates) lives in the
Deployment on top.

---

## 3. Deployment — ReplicaSets + rollouts

A **Deployment** wraps ReplicaSets and adds **versioned, controlled updates**. This is the
object you write for any stateless service (web, API, workers).

What it gives you:
- **Self-healing & scaling** (inherited from its ReplicaSet) — `kubectl scale deploy/web
  --replicas=5`.
- **Rolling updates** — ship a new image with **no downtime** (details below).
- **Rollback** — `kubectl rollout undo deploy/web` jumps back to the previous version instantly.
- **Rollout history & status** — `kubectl rollout status/history deploy/web`.

---

## 4. How a rolling update actually works (key interview topic)

When you change the Pod template (e.g., `image: web:v2`), the Deployment doesn't kill
everything at once. It:

1. Creates a **new ReplicaSet** (for v2) alongside the **old** one (v1).
2. **Gradually scales the new one up and the old one down**, a few Pods at a time, waiting for
   new Pods to become **Ready** (readiness probe!) before continuing.
3. Ends with v2 at full count, v1 at zero — but **keeps the old ReplicaSet around** (scaled to
   0) so rollback is instant.

Two knobs control the pace (`spec.strategy.rollingUpdate`):
- **`maxUnavailable`** — how many Pods may be down during the update (e.g., 25%). Lower =
  safer, slower.
- **`maxSurge`** — how many *extra* Pods may be created above desired during the update (e.g.,
  25%). Higher = faster, more resources.

The other strategy is **`Recreate`** — kill all old Pods, then start new ones (causes
downtime; used when two versions can't run at once).

**Why readiness probes matter here:** the rollout only proceeds when new Pods report *Ready*.
A bad version that never becomes Ready **stalls the rollout instead of taking down your app** —
a built-in safety net. (See Lesson 08 for probes.)

---

## 5. Self-healing in action (what to demo/say)

- **Kill a Pod** → the ReplicaSet notices count dropped and **creates a replacement** in
  seconds (new name, new IP).
- **A node dies** → its Pods are marked gone; the ReplicaSet recreates them **on healthy
  nodes**.
- **Scale** → change `replicas`; the controller adds/removes Pods to match.

This is the thermostat loop again — you never issue repair commands; you just declare the
desired count and the controller maintains it.

---

## 6. A Deployment, annotated

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
spec:
  replicas: 3                      # desired count (ReplicaSet enforces this)
  selector:
    matchLabels: { app: web }      # which Pods this Deployment owns
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 25%          # ≤25% down during a rollout
      maxSurge: 25%                # ≤25% extra during a rollout
  template:                        # the Pod recipe (changing this triggers a rollout)
    metadata:
      labels: { app: web }         # MUST match selector above
    spec:
      containers:
        - name: nginx
          image: nginx:1.27
          readinessProbe:          # rollout waits for this before moving on
            httpGet: { path: /, port: 80 }
```

**The two label blocks must match** (`selector.matchLabels` == `template.metadata.labels`) —
that's how the Deployment recognizes its own Pods. A mismatch is a classic beginner bug.

---

## 7. Useful commands

```bash
kubectl apply -f web.yaml                 # create/update from desired state
kubectl get deploy,rs,pods -l app=web     # see the whole chain
kubectl scale deploy/web --replicas=5     # scale
kubectl set image deploy/web nginx=nginx:1.27.1   # trigger a rolling update
kubectl rollout status deploy/web         # watch the rollout
kubectl rollout undo deploy/web           # roll back to previous ReplicaSet
kubectl rollout history deploy/web        # see revisions
```

---

## 🎤 Say this in the interview

- *"A Deployment manages a ReplicaSet, which keeps N identical Pods alive by label selector.
  I manage the Deployment, never the Pods."*
- *"A rolling update spins up a new ReplicaSet and shifts Pods over gradually, gated by
  **readiness probes** and bounded by **maxSurge/maxUnavailable**, keeping the old ReplicaSet
  for instant **rollback**."*
- *"Use a **Deployment** for stateless apps; the moment Pods need stable identity or their own
  disk, switch to a **StatefulSet** (Lesson 07)."*

➡️ **Next:** [04 — Config & Storage](./04_Config_And_Storage.md)
