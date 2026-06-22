# 🎯 Preparation Roadmap — what to study, in priority order

A single place to decide **what to study next** for backend / cloud-infrastructure
engineering interviews. Courses are grouped into **priority tiers** — core foundations first,
then high-value add-ons, then refreshers. Each course follows the same format:
**"The One Idea"** analogy → real details → **"Say this in the interview"** one-liners.

> **Status key:** ✅ Ready (lessons written) · 📝 Syllabus ready (outline only, lessons TBD)

---

## ✅ Ready now — completed courses

| Course | What it covers | Link |
|---|---|---|
| Go From Scratch | Go internals, concurrency, GC, tooling + interview lines | [Golang_From_Scratch/](./Golang_From_Scratch/) |
| Kubernetes From Scratch | K8s → OpenShift → OCM, 1-day crash course | [Kubernetes_From_Scratch/](./Kubernetes_From_Scratch/) |
| Kafka From Scratch | Commit log, partitions, delivery semantics, HA | [Kafka_From_Scratch/](./Kafka_From_Scratch/) |
| AWS From Scratch | Core AWS services, networking, scaling, cost | [AWS_From_Scratch/](./AWS_From_Scratch/) |
| 5G Core From Scratch | 5G NFs (AMF/SMF/UPF…) in plain English, end to end | [5G_From_Scratch/](./5G_From_Scratch/) |

---

## 🔴 Tier 1 — build first (core foundations, asked most)

> Why first: these are the foundations almost every backend / infra interview leans on —
> networking, coding (DSA), system design, and databases.

| Course | Why it matters | Status |
|---|---|---|
| **[Networking_From_Scratch](./Networking_From_Scratch/)** | The backbone of infra roles: BGP/BFD/SCTP/TCP-UDP/IPv4-IPv6/DSCP/netlink/VRF. | ✅ |
| **[DSA_From_Scratch](./DSA_From_Scratch/)** | Where coding rounds are won or lost — the most directly trainable interview skill. | ✅ |
| **[System_Design_From_Scratch](./System_Design_From_Scratch/)** | The senior-level differentiator: scaling, trade-offs, end-to-end ownership. | ✅ |
| **[Databases_From_Scratch](./Databases_From_Scratch/)** | Index types, transactions, replication/sharding across SQL & NoSQL. | ✅ |

**Suggested order:** Networking → DSA → System Design → Databases.

---

## 🟡 Tier 2 — strong value

| Course | Why it matters | Status |
|---|---|---|
| **[gRPC_From_Scratch](./gRPC_From_Scratch/)** | The default for internal service-to-service APIs; Protobuf, streaming, deadlines. | ✅ |
| **[Docker_From_Scratch](./Docker_From_Scratch/)** | Containers and **multistage builds** — a common follow-up to any K8s discussion. | ✅ |
| **[IaC_Terraform_From_Scratch](./IaC_Terraform_From_Scratch/)** | Terraform **and** CloudFormation — provisioning shows up in most infra interviews. | ✅ |
| **[LLM_For_Engineers_From_Scratch](./LLM_For_Engineers_From_Scratch/)** | LLM internals, RAG, and fine-tuning — increasingly asked of backend engineers. | ✅ |

---

## 🟢 Tier 3 — refreshers / strong-but-underused topics

| Course | Why it matters | Status |
|---|---|---|
| **[Java_Spring_Boot_From_Scratch](./Java_Spring_Boot_From_Scratch/)** | Still a default enterprise backend stack; common in mixed Java/Go shops. | ✅ |
| **[Observability_From_Scratch](./Observability_From_Scratch/)** | Metrics, logging, tracing, SLOs — how you cut MTTR and prove reliability. | ✅ |
| **[Security_From_Scratch](./Security_From_Scratch/)** | AuthN/Z, TLS/PKI, OWASP, secure SDLC — an easy way to stand out. | ✅ |

---

## ➕ Chapters to add to existing (✅) courses
- **Kubernetes:** add a `Helm` lesson and a `Monitoring / Prometheus` lesson.
- **AWS:** add a `Kinesis` lesson.
- **Go:** add a dedicated `Testing & benchmarks` lesson and a `gRPC in Go` bridge.

---

## 🗺️ How to use this during prep
1. Pick the **top unfinished Tier 1** course and read its `README.md` syllabus.
2. Work lessons in order; each ends with **"Say this in the interview"** lines — memorise those.
3. Before a specific interview, jump to the matching course (e.g. a Go round → Go + DSA;
   a design round → System Design + Databases; a 5G/networking round → 5G + Networking).
