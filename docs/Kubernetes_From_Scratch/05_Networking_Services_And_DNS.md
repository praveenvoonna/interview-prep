# 05 — Networking: Services & DNS

## 🧠 The One Idea

**Pods come and go and change IPs, so you never talk to a Pod directly — you talk to a Service,
which is a stable front door with a fixed name and IP that load-balances to whatever Pods are
healthy right now.** Think of a Service as a **company phone number**: employees (Pods) come
and go, but the number never changes, and calls get routed to whoever's available. And because
remembering IPs is painful, Kubernetes runs **built-in DNS** so you reach Services by **name**.

---

## 1. The problem Services solve

- Every Pod gets its **own IP**, but Pods are **disposable** — restart one and the IP changes.
- So how does the frontend find the backend, when the backend's Pods keep changing? **You
  can't hardcode Pod IPs.**
- A **Service** gives a **stable virtual IP (ClusterIP) + DNS name** that **never changes**,
  and automatically forwards traffic to the current set of healthy backend Pods.

**How it finds its Pods:** the same mechanism as everything else — a **label selector**. The
Service watches for Pods matching `selector` and keeps a live list of their IPs (called
**Endpoints** / EndpointSlices). Only **Ready** Pods (readiness probe) are included.

```yaml
apiVersion: v1
kind: Service
metadata: { name: backend }
spec:
  selector: { app: backend }     # send traffic to Pods labeled app=backend
  ports:
    - port: 80                    # the Service port
      targetPort: 8080            # the container port
```

---

## 2. The Service types (when to use each)

- **ClusterIP (default)** — a virtual IP reachable **only inside the cluster**. The standard
  way for in-cluster apps to talk to each other (frontend → backend). *Internal phone
  extension.*
- **NodePort** — opens the **same high port on every node** (30000–32767) and forwards to the
  Service. Reachable from **outside** via `nodeIP:nodePort`. Crude; mostly for dev/testing.
- **LoadBalancer** — provisions a **cloud load balancer** with an external IP that feeds the
  Service. The normal way to expose a service to the internet on a cloud. *Public phone
  number.* (Under the hood it builds on NodePort.)
- **ExternalName** — no proxying; just returns a **CNAME** to an external DNS name (map an
  in-cluster name to `db.example.com`).
- **Headless Service** (`clusterIP: None`) — **no** virtual IP / load-balancing; DNS returns
  the **individual Pod IPs** directly. Used when clients need to reach **specific** Pods —
  e.g., StatefulSets, databases, and peer-to-peer clusters (Lesson 07).

---

## 3. How a Service actually routes traffic (kube-proxy)

- The Service's ClusterIP is **virtual** — no process listens on it. **kube-proxy** (running
  on every node) programs the node's **iptables / IPVS** (or eBPF with Cilium) rules so that
  packets to the ClusterIP are **rewritten** to one of the backend Pod IPs.
- So load-balancing is done by **kernel networking rules on each node**, not a central proxy
  process. This is efficient and has no single bottleneck.
- The control plane keeps the backend list fresh via **EndpointSlices** (the modern, scalable
  form of Endpoints) as Pods become Ready/NotReady.

---

## 4. Cluster DNS — finding Services by name

- Kubernetes runs an internal DNS server (**CoreDNS**) so every Service gets a **DNS name**.
- A Service `backend` in namespace `shop` is reachable at:
  - `backend` (from within the same namespace),
  - `backend.shop` (from another namespace),
  - **`backend.shop.svc.cluster.local`** (the fully-qualified name).
- So your frontend just connects to `http://backend` (or `backend.shop`) — **no IPs anywhere.**
  This is **service discovery**, and it's automatic.
- For a **headless** Service, DNS returns each Pod: `pod-0.backend.shop.svc.cluster.local`.

---

## 5. Ingress — one smart front door for HTTP

Exposing each service with its own LoadBalancer is expensive. **Ingress** gives you **one
entry point** that **routes by hostname/path** to many internal Services — an **L7 (HTTP)
router / reverse proxy**.

- Example: `shop.com/api` → `api` Service, `shop.com/web` → `web` Service, all behind one IP.
- It also centralizes **TLS termination** (HTTPS certs in one place).
- **Ingress is just the rules**; you need an **Ingress Controller** (nginx, HAProxy, Traefik)
  running in the cluster to actually enforce them. No controller = Ingress does nothing.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata: { name: shop }
spec:
  rules:
    - host: shop.com
      http:
        paths:
          - path: /api
            pathType: Prefix
            backend: { service: { name: api, port: { number: 80 } } }
```

> **OpenShift note:** OpenShift had this earlier as a **Route** object (built on HAProxy),
> with easy edge/passthrough/re-encrypt TLS modes. Routes and Ingress coexist on OpenShift.
> (More in Lesson 11.) The newer, richer successor across the ecosystem is the **Gateway API**.

---

## 6. The layers, top to bottom (mental recap)

```
Internet
   │
 Ingress / Route   ← L7: route by host/path, terminate TLS  (one front door)
   │
 Service (ClusterIP) ← stable name+VIP, load-balances to Pods (kube-proxy)
   │
 Pods (Endpoints)   ← the actual app, selected by labels, must be Ready
   │
 CNI               ← gives each Pod its IP, connects Pods across nodes
```

---

## 🎤 Say this in the interview

- *"Pods are disposable and change IPs, so apps talk to a **Service** — a stable VIP + DNS
  name that load-balances to the **Ready** Pods matching its label selector."*
- *"ClusterIP for internal, NodePort/LoadBalancer to expose externally, **Headless** for
  stable per-Pod addressing (StatefulSets). kube-proxy implements it with iptables/IPVS/eBPF."*
- *"**Ingress/Route** is an L7 front door that routes by host/path and terminates TLS, so I
  don't need a load balancer per service. CoreDNS gives me name-based discovery."*

➡️ **Next:** [06 — The declarative model & reconciliation](./06_Declarative_Model_And_Reconciliation.md)
