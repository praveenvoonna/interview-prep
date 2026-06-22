# 04 — Networking & Volumes

## 🧠 The One Idea

**Containers are isolated by default — networking pokes controlled holes so they can talk, and
volumes give them durable storage that outlives the container.** A container's own filesystem is
disposable; if you want data or connectivity to survive, you wire it up explicitly.

The one-liner: **"Networks connect containers; volumes persist data beyond the container's
lifetime — the writable layer is throwaway."**

---

## 1. The network drivers

| Driver | What it does |
|---|---|
| **bridge** (default) | private virtual network on the host; containers get internal IPs |
| **host** | container shares the host's network stack (no isolation, no NAT) |
| **none** | no networking |
| **overlay** | multi-host network (Swarm/K8s) — containers across machines on one network |

On a **user-defined bridge**, containers reach each other **by name** (built-in DNS) — far better
than the legacy default bridge.

---

## 2. Port publishing

- `EXPOSE` only documents; **`-p host:container`** actually publishes.

```bash
docker run -p 8080:80 nginx   # host:8080 → container:80
```

- Without `-p`, a bridge container is reachable from sibling containers but **not** from the host's
  outside world.

---

## 3. Container-to-container communication

```bash
docker network create app-net
docker run -d --name db   --network app-net postgres
docker run -d --name api  --network app-net myapi   # connects to "db:5432" by name
```

The DNS name = the container/service name. This is exactly how `docker-compose` wires a stack.

---

## 4. Persisting data: volumes vs bind mounts

- **Volume** (`-v mydata:/var/lib/postgresql/data`): Docker-managed storage under Docker's control —
  the right choice for databases/state. Survives container removal.
- **Bind mount** (`-v $(pwd):/src`): maps a host path in — great for **local dev** (live code), not
  for prod data.
- **tmpfs:** in-memory, nothing persisted — for secrets/scratch.

Anything written to the container's **writable layer** (not a volume) is **lost** when the container
is removed.

---

## 5. docker-compose (multi-container)

```yaml
services:
  api:
    build: .
    ports: ["8080:8080"]
    depends_on: [db]
  db:
    image: postgres:16
    volumes: ["pgdata:/var/lib/postgresql/data"]
volumes:
  pgdata:
```

Compose creates a network automatically, so `api` reaches `db` by name. One file = a whole local
stack.

---

## 🎤 Say this in the interview

- *"By default containers join a bridge network; on a user-defined bridge they resolve each other by
  name via built-in DNS, and `-p` publishes a port to the host."*
- *"State goes in volumes, not the container's writable layer, which is disposable — bind mounts are
  for dev, named volumes for prod data."*
- *"For a multi-container local stack I use compose, which wires up the network and DNS names for
  me."*

➡️ **Next:** [05 — Registries & security](./05_Registries_And_Security.md)
