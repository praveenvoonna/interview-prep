# 05 — BFD & Failure Detection

## 🧠 The One Idea

**BFD is a heartbeat between two routers: "still there? still there? still there?"** BGP on its
own can take *seconds to minutes* to notice a dead neighbour (its hold timer). BFD sends tiny
packets every few **milliseconds**, so the instant the heartbeat stops, it tells BGP "this peer
is gone — re-route now." **BFD is a fast, protocol-independent liveness detector.**

The one-liner: **"BFD gives sub-second failure detection so routing protocols converge in
milliseconds instead of seconds."**

---

## 1. Why BFD exists

- BGP's own keepalive/hold timers are coarse (default hold ~90s; minutes to notice loss).
- Hardware link-down isn't always signalled (e.g. a dead device two hops away, or an L2 switch in
  between hides the failure).
- **BFD** runs a lightweight session and notifies its **clients** (BGP, OSPF, static routes) the
  moment liveness is lost — so they can withdraw routes and converge fast.

---

## 2. How it works

- Two endpoints exchange **BFD control packets** at a negotiated interval (e.g. every 50ms).
- If **N consecutive packets are missed** (the *detect multiplier*, e.g. 3), the session is
  declared **down** → notify clients.
- **Modes:** *asynchronous* (continuous heartbeats, the common one) and *demand*; **echo** mode
  loops packets back through the peer's data path to test forwarding.
- It's **protocol-agnostic** — the same BFD session can back BGP *and* static routes.

---

## 3. Source ports & RFC 5881 (your hardening)

- BFD-for-single-hop is defined by **RFC 5881**: control packets use **UDP destination 3784** and
  a **source port from the ephemeral range 49152–65535**.
- Real work you did:
  - Allocate source ports from that ephemeral range with a **randomized → sequential fallback on
    `EADDRINUSE`** (so a taken port doesn't fail the session).
  - **Cap retries** for non-port errors so you don't spin forever.
  - Added a **memory-cache layer** to limit **etcd watch notifications** under load — protecting
    the datastore when many sessions churn.

These are concrete reliability stories: standards-compliant, defensive, and load-aware.

---

## 4. BFD + BGP together

```
BFD heartbeat stops  →  BFD session DOWN  →  notify BGP
        →  BGP withdraws routes via that peer  →  traffic re-routes
```

Without BFD: wait for BGP hold timer (seconds+). With BFD: **milliseconds**. That delta is the
difference between a blip and a customer-visible outage — which is why it's a carrier requirement.

---

## 5. Tuning trade-off

- **Aggressive timers** (e.g. 50ms × 3) → fastest detection, but risk **false positives** under
  CPU/scheduling jitter, causing flaps.
- **Relaxed timers** → fewer false positives, slower detection.
- The right answer is "tune to the SLA and the platform's jitter; pair with damping to avoid flap
  storms."

---

## 🎤 Say this in the interview

- *"BFD is a sub-second, protocol-independent liveness heartbeat: miss N packets and it tells BGP
  the peer is down, so convergence drops from seconds to milliseconds."*
- *"Per RFC 5881 single-hop BFD uses dest port 3784 and an ephemeral source port; I allocate from
  that range with a randomized-then-sequential fallback on `EADDRINUSE` and capped retries."*
- *"I added a memory cache to throttle etcd watch notifications under session churn — protecting
  the datastore under load."*

➡️ **Next:** [06 — DSCP / QoS](./06_DSCP_And_QoS.md)
