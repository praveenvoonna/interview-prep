# 07 — Linux Networking Internals

## 🧠 The One Idea

**The kernel owns the network; your control plane is the kernel's manager, giving orders over a
phone line called netlink.** Routes, interfaces and addresses all live in the kernel. Your Go code
doesn't move packets — it **tells the kernel** "add this route", "bring up this interface", "bind
here" via **netlink**, and the kernel does the forwarding. This is exactly how a BGP speaker turns
learned routes into real forwarding.

The one-liner: **"The control plane programs kernel forwarding state over netlink; the data plane
(kernel) does the actual packet movement."**

---

## 1. netlink — talking to the kernel

- **netlink** is the socket-based API between user space and the kernel for networking config:
  add/delete routes, addresses, interfaces, neighbours, rules.
- It's how `ip route add …` works under the hood; in Go you use libraries like `vishvananda/netlink`.
- Your BGP speaker learns a route via BGP, then **installs it into the kernel FIB over netlink** —
  that's the bridge from control plane to data plane.

---

## 2. Interfaces: physical, dummy, loopback

- **Physical** (`eth0`) — a real NIC; goes down when the cable/link fails.
- **Loopback** (`lo`, `127.0.0.1`/`::1`) — always up, local only.
- **Dummy** — a virtual interface with a **stable IP not tied to any NIC**. It's always up.
  - **Why you used it:** binding the **GoBGP server (and its gRPC/CLI plane) to a dummy
    interface** means the management plane stays reachable even when a physical interface flaps —
    the decoupling fix from Lesson 04.

---

## 3. Routing tables & rules

- The kernel can hold **multiple routing tables**; **ip rules** (policy routing) pick which table
  a packet uses based on source, mark, etc.
- This is the mechanism underneath **VRFs** (next section) and multi-homing.
- `ip route show`, `ip rule show`, `ip addr` are the inspection commands to mention.

---

## 4. VRFs — virtual routing tables

- **VRF (Virtual Routing and Forwarding)** = multiple **independent routing tables** on one host,
  so the *same* IP/prefix can exist in two isolated contexts without clashing.
- Think "multi-tenant routing": tenant A and tenant B can both use `10.0.0.0/16` with no overlap
  because each lives in its own VRF.
- In 5G/telecom this isolates network slices / customer traffic on shared infrastructure.

---

## 5. Putting it together (a BGP speaker)

```
BGP peer announces 10.0.0.0/16
   → control plane (GoBGP) receives UPDATE
   → selects best path (Lesson 04)
   → installs route into kernel FIB via netlink
   → kernel forwards matching packets (data plane)
BFD says peer is down (Lesson 05)
   → control plane withdraws → netlink deletes route → traffic re-routes
```

The control plane is **brains** (decide); the kernel is **muscle** (forward); **netlink** is the
nervous system between them. Binding to **dummy** interfaces and isolating with **VRFs** keeps it
robust and multi-tenant.

---

## 🎤 Say this in the interview

- *"The kernel owns forwarding; my control plane programs it over netlink — BGP selects a route,
  then I install it into the kernel FIB, and the kernel does the data-plane forwarding."*
- *"I bind the BGP/gRPC management plane to a dummy interface — a stable always-up IP — so the
  control plane survives physical-link flaps."*
- *"VRFs are independent routing tables on one host, so overlapping prefixes coexist — that's how
  you isolate tenants or slices on shared infrastructure."*

➡️ **Next:** [08 — Hands-on lab](./08_Hands_On_Lab.md)
