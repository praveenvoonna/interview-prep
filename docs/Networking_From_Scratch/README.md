# 🌐 Networking From Scratch — A Crash Course (in plain English)

A from-zero deep dive into the networking that **cloud-infrastructure roles run on**:
the models, IP, the transport protocols (TCP/UDP/**SCTP**), routing (**BGP**), failure
detection (**BFD**), QoS (**DSCP**), and the Linux plumbing (netlink, dummy/loopback, VRF)
that BGP-speaker work depends on.

> **How to read this:** go in order, 00 → 09. Each lesson starts with a plain-English analogy
> (**"The One Idea"**), then the real details, then **"Say this in the interview"** one-liners
> you can repeat almost verbatim.

> **Status:** 📝 *Syllabus ready — lessons to be written.* This README is the outline and the
> reference you can study from today.

---

## 📂 The lessons (planned, read in order)

### Part A — The mental model
- **00 — OSI & TCP/IP models** — the 7/4 layers, what each does, where your work lives.
- **01 — IP addressing & subnets** — IPv4 vs IPv6, CIDR, prefixes, why IPv6-only matters.

### Part B — Transport
- **02 — TCP vs UDP** — connections, handshake, flow/congestion control, when to use which.
- **03 — SCTP** — multi-homing, multi-streaming, associations; why telco/5G uses it; the
  `ishidawataru/sctp` Go library and your `SyscallConn()` contribution.

### Part C — Routing & reachability (your bread and butter)
- **04 — Routing & BGP** — prefixes, AS paths, eBGP/iBGP, route advertisement/withdrawal,
  policies, soft-reset vs hard-reset, GoBGP.
- **05 — BFD & failure detection** — sub-second liveness, why it pairs with BGP, RFC 5881
  ephemeral source ports.

### Part D — Quality & the Linux layer
- **06 — DSCP / QoS** — marking, traffic classes, socket options; your end-to-end DSCP work.
- **07 — Linux networking internals** — netlink, dummy/loopback interfaces, VRFs, routing
  tables; how the control plane talks to the kernel.

### Part E — Practice
- **08 — Hands-on lab** — `tcpdump`/`pcap`, inspect a TCP handshake, watch a BGP session.
- **09 — Interview Q&A flashcards** — rapid-fire questions with crisp answers.

---

## 🧠 The whole course in three sentences
1. **The network is layered:** IP moves packets between hosts; transport (TCP/UDP/SCTP) gives
   apps the delivery semantics they need on top.
2. **Routing decides the path:** BGP advertises and withdraws prefixes between peers, and BFD
   tells BGP *fast* when a peer died so routes can be re-converged.
3. **The control plane programs the kernel:** via netlink it installs routes, binds servers to
   dummy interfaces, and isolates traffic with VRFs — exactly what a BGP speaker does.

---

## 🔗 Companion material
- **[5G Core From Scratch](../5G_From_Scratch/)** — what consumes this networking.

When the lessons are written, start at **00**.
