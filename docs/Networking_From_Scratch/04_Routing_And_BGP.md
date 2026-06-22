# 04 — Routing & BGP

## 🧠 The One Idea

**BGP is how the independent kingdoms of the internet tell each other "to reach me, come this
way."** Each network (an **Autonomous System**) gossips to its neighbours which address blocks it
can reach and the path to get there. Nobody has the whole map; everyone just trusts and forwards
their neighbours' directions. **BGP is the internet's distributed, policy-driven routing gossip.**

The one-liner: **"BGP exchanges reachability (prefixes + AS-path) between autonomous systems and
picks routes by policy, not just shortest distance."**

---

## 1. Routing fundamentals

- A **route** = a destination **prefix** (`10.0.0.0/16`) + a **next-hop** (who to send it to).
- A router's **routing table (RIB)** holds candidate routes; the **FIB** is the compiled
  forwarding table the kernel actually uses.
- **Longest-prefix match wins:** `/24` beats `/16` for an address in both.
- **Static routes** are hand-configured; **dynamic routing** (BGP, OSPF) learns them automatically.

---

## 2. BGP basics

- **Autonomous System (AS):** a network under one administrative control, with an **ASN**.
- **eBGP** = between different ASes (the internet); **iBGP** = within one AS.
- Runs over **TCP port 179** — so BGP itself relies on a reliable transport.
- Peers **establish a session**, exchange their full tables once, then send **incremental
  updates**: `UPDATE` messages that **announce** or **withdraw** prefixes.

---

## 3. How BGP picks a route

BGP isn't shortest-path; it's **policy + attributes**, evaluated in order. The ones to name:

1. **Local preference** (prefer our preferred exit).
2. **AS-path length** (fewer ASes ≈ closer) — the famous tie-breaker.
3. **Origin, MED**, eBGP over iBGP, lowest router-ID.

The takeaway: **operators shape traffic with policy**, which is why BGP is "policy routing".

---

## 4. Announce, withdraw, and convergence

- **Announce:** "I can reach `10.0.0.0/16`" → peers install it.
- **Withdraw:** "I can no longer reach it" → peers remove it. **Bugs here = blackholes.** (You
  fixed cases where routes weren't withdrawn on policy removal or refreshed on interface restart.)
- **Convergence:** the time for the whole network to agree after a change. Faster failure
  detection (BFD, Lesson 05) → faster convergence.
- **Soft-reset vs hard-reset:** applying a new policy with a **soft-reset** re-evaluates routes
  **without tearing down the TCP session** — avoiding churn. You switched dynamic policy
  add/update/delete to soft-reset only.

---

## 5. GoBGP and your control-plane work

- **[GoBGP](https://github.com/osrg/gobgp)** is a BGP implementation in Go with a gRPC management
  API — what your service drives.
- Real things you did here, in BGP terms:
  - **Decoupled the management/gRPC plane from the data plane** by binding GoBGP to a dedicated
    **dummy interface** (configurable IP/port) → CLI stays responsive even when a physical link
    is down.
  - **IPv6-only bring-up** of the BGP server.
  - A **route-add retry framework** with backoff + **static-route fallback** when defaults are
    pre-configured — killing intermittent install failures.

---

## 🎤 Say this in the interview

- *"BGP exchanges prefixes and AS-paths between autonomous systems over TCP/179 and selects routes
  by policy — local-pref, then AS-path length — not pure shortest path."*
- *"Announce adds reachability, withdraw removes it; missing withdrawals cause blackholes, so I
  fixed cases where routes weren't withdrawn on policy removal."*
- *"I use soft-reset to re-apply policy without tearing down the session, and I decoupled the
  GoBGP management plane onto a dummy interface so the CLI stays up when a physical link flaps."*

➡️ **Next:** [05 — BFD & failure detection](./05_BFD_And_Failure_Detection.md)
