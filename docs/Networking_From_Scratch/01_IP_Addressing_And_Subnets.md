# 01 — IP Addressing & Subnets

## 🧠 The One Idea

**An IP address is a postal address: a network part (the city) and a host part (the house).**
The **subnet mask** is the line that tells you where the city ends and the house begins. Routers
only care about the city (the network/prefix); the last hop cares about the house. Subnetting is
just **drawing that line in different places** to carve one big address space into many.

The one-liner: **"An address is network-prefix + host; the mask says where the split is."**

---

## 1. IPv4 basics

- 32 bits, written as four octets: `192.168.10.25`.
- **CIDR notation** `192.168.10.0/24` = the first **24 bits are the network**, last 8 are hosts.
  `/24` → 256 addresses (254 usable; `.0` network, `.255` broadcast).
- Smaller prefix number = bigger network. `/16` = 65 536 addresses; `/32` = a single host.

**Private ranges (RFC 1918)** — not routable on the internet, used inside VPCs/datacentres:
`10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`.

---

## 2. Subnetting — splitting the space

To split `10.0.0.0/16` into four equal subnets, borrow 2 host bits → `/18`:
`10.0.0.0/18`, `10.0.64.0/18`, `10.0.128.0/18`, `10.0.192.0/18`.

- More prefix bits → more subnets, fewer hosts each.
- This is exactly how you carve a **VPC** into subnets (Networking ↔ AWS VPC lesson).

Quick math: hosts per subnet = `2^(32 − prefix) − 2` (minus network + broadcast).

---

## 3. IPv6 — why it exists

- 128 bits, written in hex groups: `2001:db8::1`. Effectively unlimited addresses.
- `::` compresses one run of zero groups. `/64` is the standard subnet size.
- **No broadcast** and **no NAT needed** — every device can have a globally unique address.
- **Link-local** `fe80::/10` addresses auto-configure on every interface.

---

## 4. IPv4 vs IPv6 and "IPv6-only"

| | IPv4 | IPv6 |
|---|---|---|
| Size | 32-bit | 128-bit |
| NAT | common | usually none |
| Config | DHCP | SLAAC / DHCPv6 |
| Broadcast | yes | no (multicast) |

**Why this matters:** 5G NFs increasingly deploy **IPv6-only**. Supporting a service
that binds on an interface with *only* an IPv6 address (no IPv4 at all) means handling
address-family selection correctly — a common gap in BGP bring-up code.

---

## 5. Special addresses to know

- `127.0.0.1` / `::1` — **loopback** (talk to yourself; used for local/control-plane binding).
- `0.0.0.0` — "any/unspecified" (bind on all interfaces).
- **Dummy/loopback interfaces** — a stable IP not tied to a physical NIC; binding your BGP server
  to one keeps the control plane reachable even when a physical link flaps (Lesson 07).

---

## 🎤 Say this in the interview

- *"CIDR is prefix + host: `/24` means 24 network bits and 256 addresses. Subnetting is just
  moving that boundary to trade subnet count against hosts-per-subnet."*
- *"IPv6 is 128-bit with no NAT and no broadcast; `/64` is the standard subnet. We support
  IPv6-only bring-up for 5G NFs, which means getting address-family selection right."*
- *"Binding a service to a dummy/loopback IP decouples it from any physical NIC, so the control
  plane stays reachable when a link goes down."*

➡️ **Next:** [02 — TCP vs UDP](./02_TCP_vs_UDP.md)
