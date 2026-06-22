# 04 — Config & Storage

## 🧠 The One Idea

**Keep three things separate: the app (image), its settings (config), and its data (storage).**
You bake the app once into an image, then inject *settings* with **ConfigMaps/Secrets** and
attach *data* with **Volumes/PVCs** at runtime. That way the same image runs in dev, test, and
prod — only the injected pieces change. *Analogy: a game console (image) + a settings menu
(ConfigMap) + a memory card that survives power-off (PersistentVolume).*

---

## Part 1 — Configuration

### ConfigMaps — non-secret settings
- A **ConfigMap** stores **plain configuration** as key/value pairs (URLs, feature flags, an
  entire config file). It keeps config **out of the image**.
- You consume it two ways:
  - as **environment variables** (`env` / `envFrom`), or
  - as **files mounted into the container** (a volume — good for whole config files).

```yaml
apiVersion: v1
kind: ConfigMap
metadata: { name: app-config }
data:
  LOG_LEVEL: "info"
  config.yaml: |
    server:
      port: 8080
```

### Secrets — sensitive settings
- A **Secret** is just like a ConfigMap but for **sensitive** data (passwords, tokens, TLS
  keys, registry creds). Consumed the same ways (env vars or mounted files).
- **Important nuance to say out loud:** Secret values are only **base64-encoded, not
  encrypted**, by default. Base64 is *encoding* (reversible), not security. Real protection
  comes from: **encryption-at-rest in etcd**, **RBAC** restricting who can read Secrets, and
  external managers (Vault, External Secrets Operator).
- Prefer **mounted files** over env vars for secrets (env vars can leak via logs/`/proc`).

### The big rule
**Changing a ConfigMap/Secret does NOT automatically restart Pods.**
- **Mounted as files** → the file updates eventually (with a delay), but the app must re-read
  it.
- **Injected as env vars** → values are fixed at Pod start; you must **restart/roll** the Pod
  to pick up changes (`kubectl rollout restart deploy/web`).

---

## Part 2 — Storage

Containers are **ephemeral**: when a container restarts, its writable layer is wiped. For
anything that must **survive**, you need a Volume.

### Volumes — storage attached to a Pod
- A **Volume** is storage mounted into a Pod's containers. Its **lifetime depends on the
  type**:
  - **`emptyDir`** — scratch space that lives **as long as the Pod** (shared between its
    containers); gone when the Pod dies. Good for caches/temp.
  - **`configMap` / `secret`** — inject config/secrets as files (Part 1).
  - **`persistentVolumeClaim`** — real durable storage that **outlives the Pod** (below).

### The PV / PVC / StorageClass triangle (key model)
This is how Kubernetes does durable storage — and a guaranteed interview question.

- **PersistentVolume (PV) — the supply.** An actual piece of storage in the cluster (a cloud
  disk, an NFS share). Think **"a physical disk available to be claimed."**
- **PersistentVolumeClaim (PVC) — the demand.** A Pod's *request* for storage: "I need 10Gi,
  read-write-once." Think **"a ticket asking for a disk."**
- **StorageClass — the vending machine (dynamic provisioning).** Instead of an admin
  pre-creating PVs, a StorageClass **auto-creates a PV on demand** when a PVC appears (e.g.,
  calls AWS to make an EBS volume). This is how it works in practice.

**The flow:** Pod → references a **PVC** → PVC matches/triggers a **PV** (often via a
**StorageClass**) → the disk is mounted into the Pod. If the Pod moves to another node, the
**PVC follows it**, so the data follows it.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata: { name: data }
spec:
  accessModes: ["ReadWriteOnce"]   # mounted read-write by a single node
  storageClassName: standard       # which "vending machine" to use
  resources:
    requests: { storage: 10Gi }
```

### Access modes (know the three)
- **RWO (ReadWriteOnce)** — read-write by **one node** at a time (most block storage; the
  common case).
- **ROX (ReadOnlyMany)** — read-only by many nodes.
- **RWX (ReadWriteMany)** — read-write by many nodes (needs shared filesystems like NFS/CephFS).

### Reclaim policy
When a PVC is deleted, what happens to the PV/data? **`Delete`** (destroy the disk — common
for dynamic cloud volumes) vs **`Retain`** (keep the data for manual recovery).

---

## 3. How it ties to StatefulSets (preview)
A Deployment's Pods usually share **no** durable storage. A **StatefulSet** (Lesson 07) gives
**each Pod its own PVC** via `volumeClaimTemplates`, so Pod-0 always re-attaches to *its* disk —
that's how databases keep their data across restarts.

---

## 🎤 Say this in the interview

- *"I separate the app (image) from its config (ConfigMap/Secret) and its data (Volume/PVC) so
  the same image runs everywhere."*
- *"Secrets are base64-**encoded**, not encrypted — real protection is etcd encryption-at-rest
  plus RBAC; for production I'd use Vault or the External Secrets Operator."*
- *"Durable storage = **PVC (demand)** binds to a **PV (supply)**, usually auto-provisioned by
  a **StorageClass**. Access modes are RWO/ROX/RWX; RWO is the common one."*
- *"Updating a ConfigMap doesn't restart Pods — for env-var config I `rollout restart`."*

➡️ **Next:** [05 — Networking: Services & DNS](./05_Networking_Services_And_DNS.md)
