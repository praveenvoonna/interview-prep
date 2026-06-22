# 06 — DSCP / QoS

## 🧠 The One Idea

**DSCP is a priority sticker on every packet.** On a busy road, ambulances get to jump the queue
because they're marked. QoS is the network deciding which packets are "ambulances" (low-latency
voice/signalling) and which are "delivery vans" (bulk backups) when the road is congested. The
**DSCP field** in the IP header is that sticker; routers read it and put packets in the right
queue.

The one-liner: **"DSCP marks each packet's priority class in the IP header so routers can queue
and drop traffic differently under congestion."**

---

## 1. What QoS actually is

QoS only matters **when there's contention**. With infinite bandwidth everything is fast; under
load, QoS decides **who waits and who drops**. The levers:

- **Classification & marking** — tag packets with a class (DSCP).
- **Queuing** — separate queues per class; priority queue for real-time.
- **Policing/shaping** — cap or smooth a class's rate.
- **Dropping** — choose what to drop first (e.g. WRED) when buffers fill.

---

## 2. DSCP in the IP header

- The IPv4 **ToS** byte / IPv6 **Traffic Class** byte holds a **6-bit DSCP** value (0–63) plus 2
  ECN bits.
- Standard code points: **EF** (Expedited Forwarding, 46) for voice/real-time; **AF** classes
  (Assured Forwarding) with drop precedences; **CS** (Class Selector) values; **0** = best-effort.
- **Per-Hop Behaviour (PHB):** each router maps a DSCP value to a queuing/drop behaviour. DSCP is
  end-to-end *marking*; the actual treatment is configured per device.

---

## 3. Setting DSCP from code (your work)

- DSCP is set as a **socket option**: `IP_TOS` (IPv4) / `IPV6_TCLASS` (IPv6).
- You implemented **DSCP marking for SCTP end-to-end** — config schema, socket options, both
  server and client paths, sample apps and tests — a ~2.3k-LOC change across 26 files used by NF
  traffic classes.
- This depended on **`SyscallConn()`** (Lesson 03) to reach the raw socket and set the option on
  SCTP connections that otherwise didn't expose it.

So the chain is: *add the raw-conn hook upstream → use it to mark SCTP traffic with DSCP →
NF traffic classes get the right network priority.*

---

## 4. Why it matters in 5G/telecom

- Signalling and voice must stay low-latency even when the link is saturated with user data.
- Marking control-plane / signalling traffic as **EF/high-priority** means it keeps flowing while
  bulk traffic queues — directly affecting call quality and reliability.
- Misconfigured DSCP (or stripping marks at a boundary) is a classic cause of "voice is choppy
  under load" incidents.

---

## 5. Gotchas

- Marks can be **rewritten or cleared** at administrative boundaries (ISPs often reset DSCP) — so
  end-to-end QoS needs a **trust boundary** agreement.
- DSCP is **advisory**: it does nothing unless routers are configured with matching PHBs/queues.
- Don't confuse **DSCP** (L3 marking) with **802.1p** (L2 CoS) — they're different layers.

---

## 🎤 Say this in the interview

- *"DSCP is a 6-bit priority field in the IP header; routers map it to per-hop queuing/drop
  behaviour, so QoS only bites under congestion — it decides who waits and who drops."*
- *"I implemented DSCP marking for SCTP end-to-end via socket options (`IP_TOS`/`IPV6_TCLASS`),
  which required the `SyscallConn()` hook I added upstream to reach the raw socket."*
- *"Marks are advisory and can be reset at boundaries, so end-to-end QoS needs a trust agreement
  and matching PHB config on each hop."*

➡️ **Next:** [07 — Linux networking internals](./07_Linux_Networking_Internals.md)
