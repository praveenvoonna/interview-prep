# 02 — Pods, the Atom

## 🧠 The One Idea

**A Pod is the smallest thing Kubernetes runs — think of it as a single "logical machine"
that wraps one container (sometimes a few that must live together).** You never hand
Kubernetes a bare container; you hand it a Pod. The key insight: **containers in the same Pod
share one IP address and can talk over `localhost`, like processes on the same computer.**
Most Pods hold exactly one container — the multi-container case is for tightly-coupled helpers.

---

## 1. What a Pod actually is

- The **smallest deployable unit** in Kubernetes. The scheduler schedules **Pods**, not
  containers.
- A Pod is a **wrapper around one or more containers** that:
  - **share one network namespace** → one **Pod IP**, shared port space, reach each other on
    `localhost`;
  - **can share storage volumes** (mounted into multiple containers);
  - are **always co-located on the same node** and **live and die together**.
- **Pods are mortal and disposable.** They're not repaired — if a Pod dies, a controller
  creates a **brand-new** Pod (new name, new IP). *Never get attached to a specific Pod.*
  This is why we use Services (stable address) and controllers (recreate them).

**Why a Pod and not just a container?** Sometimes a main app needs a tiny helper glued to it
(a log shipper, a proxy). Putting them in one Pod lets them share localhost and disk and be
scheduled together — the helper is a "**sidecar**."

---

## 2. The Pod lifecycle (phases)

`status.phase` — the high-level state:

- **Pending** — accepted by the cluster but not running yet (waiting to be scheduled, or
  pulling the image).
- **Running** — bound to a node, at least one container is running.
- **Succeeded** — all containers exited successfully (for run-to-completion Pods).
- **Failed** — all containers terminated, at least one failed.
- **Unknown** — the node stopped reporting (e.g., node lost).

**Container-level states** you'll see in `kubectl describe pod` (more useful for debugging):
- **Waiting** with a reason — `ImagePullBackOff` (can't pull image),
  `CrashLoopBackOff` (keeps crashing, K8s backs off restarting it).
- **Running** — up and healthy.
- **Terminated** with an exit code/reason — e.g. `OOMKilled` (used more memory than its
  limit), `Completed` (exit 0), `Error`.

**`restartPolicy`** (how the kubelet restarts *containers within* a Pod):
- `Always` (default — for long-running apps/Deployments),
- `OnFailure` (restart only on non-zero exit — for Jobs),
- `Never`.
> Note: this restarts containers **in place**; it does **not** reschedule the Pod to another
> node. Replacing a whole dead Pod is a **controller's** job.

---

## 3. Multi-container patterns (know the names)

Most Pods are single-container. When they aren't, it's usually one of these:

- **init containers** — run **to completion, in order, BEFORE** the app containers start.
  Used for setup: wait for a dependency, run a DB migration, fetch config. A failed init
  container blocks the Pod from starting. *Analogy: the prep cook who must finish before the
  line cook starts.*
- **sidecar** — a helper running **alongside** the main container for its whole life: a log
  shipper, a service-mesh proxy (Envoy), a config reloader. (K8s 1.28+ has native
  "restartable sidecars" implemented as a special init container.) *Analogy: a motorcycle
  sidecar — separate seat, same bike.*
- **ambassador / adapter** — sidecar variants: an *ambassador* proxies the app's outbound
  connections; an *adapter* reshapes the app's output (e.g., into a metrics format).
- **ephemeral / debug containers** — temporarily injected into a **running** Pod to debug it
  (`kubectl debug -it <pod> --image=busybox`), great when the app image has no shell.

---

## 4. The bits inside a Pod spec you'll actually set

- **probes** (health checks — see Lesson 08): `livenessProbe` (restart if unhealthy),
  `readinessProbe` (remove from Service if not ready), `startupProbe` (for slow starters).
- **resources**: `requests` (guaranteed minimum, used for scheduling) and `limits` (hard cap)
  for CPU/memory.
- **env / envFrom**: environment variables, often pulled from ConfigMaps/Secrets (Lesson 04).
- **volumeMounts**: where to mount storage in the container.
- **securityContext**: run as non-root, drop Linux capabilities, read-only filesystem.
- **nodeSelector / affinity / tolerations**: influence *where* it can be scheduled (Lesson 08).
- **terminationGracePeriodSeconds**: how long to allow a graceful shutdown before SIGKILL.

---

## 5. A minimal Pod (so you've seen the raw thing)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello
  labels: { app: hello }     # labels = sticky notes used by Services/controllers
spec:
  containers:
    - name: web
      image: nginx:1.27
      ports:
        - containerPort: 80
      resources:
        requests: { cpu: "100m", memory: "64Mi" }
        limits:   { cpu: "250m", memory: "128Mi" }
```

> **In real life you almost never write a bare Pod like this.** You write a **Deployment**
> (next lesson), which creates and manages Pods for you so they self-heal and roll out
> safely. Bare Pods don't come back if deleted.

---

## 🎤 Say this in the interview

- *"A Pod is the smallest deployable unit — one or more containers that share an IP, network,
  and storage, and are always co-scheduled. Most Pods have one container."*
- *"Pods are disposable; they're never repaired, only replaced — that's why we rely on
  controllers and Services rather than individual Pods."*
- *"Multi-container patterns: **init** containers run-to-completion first; **sidecars** run
  alongside (logging, mesh proxy). `CrashLoopBackOff` and `OOMKilled` are the states I look
  for when debugging."*

➡️ **Next:** [03 — Controllers & Deployments](./03_Controllers_And_Deployments.md)
