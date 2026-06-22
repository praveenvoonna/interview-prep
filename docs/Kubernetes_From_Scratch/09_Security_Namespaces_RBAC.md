# 09 — Security: Namespaces & RBAC

## 🧠 The One Idea

**A cluster is a shared office building. Namespaces are the separate floors/offices that keep
teams from bumping into each other; RBAC is the keycard system that controls who can open which
door.** Namespaces give you **organization and isolation**; RBAC gives you **least-privilege
access**. Every actor — human or Pod — has an **identity**, and RBAC decides what that identity
is allowed to do.

---

## Part 1 — Namespaces (organization & isolation)

### What they are
- A **Namespace** is a **virtual cluster inside the cluster** — a scope to group and isolate
  objects (Pods, Services, Deployments, ConfigMaps…). Names must be unique **within** a
  namespace, not across them (you can have `web` in `team-a` and `web` in `team-b`).
- Used to separate **teams, environments (dev/stage), or apps**, and to attach **quotas and
  access rules** per group.

### Namespaced vs cluster-scoped objects
- **Namespaced** (live inside a namespace): Pods, Deployments, Services, ConfigMaps, Secrets,
  PVCs, Roles, RoleBindings, ServiceAccounts.
- **Cluster-scoped** (no namespace, global): **Nodes, PersistentVolumes, Namespaces
  themselves, StorageClasses, ClusterRoles, CRDs**. *Rule of thumb: infrastructure-level
  things are cluster-scoped; app-level things are namespaced.*

### Default namespaces you'll meet
- `default` (where stuff goes if you don't specify), `kube-system` (control-plane components —
  don't touch), `kube-public`, `kube-node-lease`. (OpenShift adds `openshift-*` namespaces and
  calls a namespace a **Project**.)

### Per-namespace governance (name-drop these)
- **ResourceQuota** — cap total CPU/memory/object counts a namespace can consume (stop one team
  starving the cluster).
- **LimitRange** — set **default** and **min/max** requests/limits per Pod/container in a
  namespace.
- **NetworkPolicy** — firewall rules scoped to a namespace (who can talk to whom).

### Cross-namespace access
- A Service is reachable across namespaces by its longer DNS name:
  `backend.team-a.svc.cluster.local` (Lesson 05).
- **Gotcha:** a namespace stuck **`Terminating`** usually means an object in it has a
  **finalizer** that hasn't completed — check for stuck resources/finalizers.

---

## Part 2 — Identity: ServiceAccounts

- **Users** (humans) authenticate from outside (certs, OIDC, tokens) — Kubernetes has no
  built-in "user" object; that's handled by the auth layer.
- **ServiceAccounts** are **identities for Pods** (in-cluster workloads). Every Pod runs as a
  ServiceAccount (the namespace's `default` SA if you don't specify one).
- A Pod's SA token is mounted in, so the app can call the **Kubernetes API** as that identity.
  This is how a controller/operator authenticates to the cluster.
- **Best practice:** give each workload **its own** ServiceAccount with **only** the permissions
  it needs — don't reuse `default` and don't over-grant.

---

## Part 3 — RBAC (Role-Based Access Control)

RBAC answers: **"can this identity perform this action on this resource?"** It's built from
just **four object types**, in two pairs:

### The permissions (what is allowed)
- **Role** — a set of **allowed verbs on resources, within ONE namespace**. E.g., "get/list/
  watch Pods in `team-a`."
- **ClusterRole** — the same idea but **cluster-wide** (or for cluster-scoped resources like
  nodes). Can be reused across namespaces.

A rule looks like: **verbs** (`get`, `list`, `watch`, `create`, `update`, `delete`) on
**resources** (`pods`, `deployments`) in **apiGroups**.

### The bindings (who gets the permissions)
- **RoleBinding** — grants a Role (or ClusterRole) to a subject **within one namespace**.
- **ClusterRoleBinding** — grants a ClusterRole to a subject **across the whole cluster**.

**Subjects** = a User, a Group, or a **ServiceAccount**.

### The mental model
```
Subject (user / group / ServiceAccount)
        │  is granted by a
   (Cluster)RoleBinding
        │  which references a
     (Cluster)Role
        │  which lists
   allowed verbs on resources
```

*Analogy:* a **Role** is a **job description** ("can read invoices"); a **RoleBinding** is the
**employment contract** that assigns that job description to a specific person. The role alone
does nothing until it's bound.

### Key principles
- **Additive only** — RBAC has **no "deny" rules**; you grant permissions, and anything not
  granted is denied. (Denial/constraint is done by admission controllers like
  Gatekeeper/OPA — see Lesson 10/governance.)
- **Least privilege** — grant the minimum verbs/resources/scope. Avoid `cluster-admin` and
  wildcard `*` except for true admins.
- **ClusterRole + RoleBinding combo** — define a permission set **once** as a ClusterRole, then
  bind it **per namespace** with RoleBindings (a common, clean pattern).

### Tiny example
```yaml
# Role: read-only on Pods in this namespace
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata: { namespace: team-a, name: pod-reader }
rules:
  - apiGroups: [""]
    resources: ["pods"]
    verbs: ["get", "list", "watch"]
---
# Binding: give that Role to a ServiceAccount
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata: { namespace: team-a, name: read-pods }
subjects:
  - kind: ServiceAccount
    name: my-app
    namespace: team-a
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

### Quick debugging
```bash
kubectl auth can-i list pods --as=system:serviceaccount:team-a:my-app -n team-a
```
`can-i` tells you instantly whether an identity is allowed an action — the fastest way to debug
"forbidden" errors.

---

## 🎤 Say this in the interview

- *"**Namespaces** isolate and organize; some objects are cluster-scoped (Nodes, PVs, CRDs),
  most are namespaced. I attach **ResourceQuota/LimitRange/NetworkPolicy** per namespace."*
- *"**ServiceAccounts** are identities for Pods; each workload should have its own with minimal
  rights."*
- *"**RBAC** = Role/ClusterRole (the permissions) + RoleBinding/ClusterRoleBinding (who gets
  them). It's **additive, no deny**, so I design for **least privilege** and verify with
  `kubectl auth can-i`."*

➡️ **Next:** [10 — Extending Kubernetes: CRDs & Operators](./10_Extending_K8s_CRDs_Operators.md)
