# 03 — SCTP

## 🧠 The One Idea

**SCTP is TCP with two superpowers: backup phone lines and multiple conversations at once.**
Like TCP it's reliable and connection-oriented, but a single SCTP "association" can run over
**several IP addresses** (so it survives one path dying) and can carry **multiple independent
message streams** (so one slow stream doesn't block the others). That's why telecom — and 5G —
love it.

The one-liner: **"SCTP = reliable like TCP, but multi-homed and multi-streamed, and message-
oriented instead of byte-oriented."**

---

## 1. What SCTP fixes about TCP

| Problem in TCP | SCTP's answer |
|---|---|
| Single path — one NIC dies, connection dies | **Multi-homing**: multiple IPs per endpoint, automatic failover |
| Head-of-line blocking — one lost byte stalls everything | **Multi-streaming**: independent ordered streams |
| Byte stream — app must frame messages | **Message-oriented**: preserves message boundaries |

---

## 2. Associations, multi-homing, multi-streaming

- An **association** is SCTP's "connection" — but between *sets* of addresses, not single IPs.
- **Multi-homing:** each endpoint advertises several IPs; one is primary, others are backups.
  **BFD-like heartbeats** monitor paths and fail over without tearing down the association.
- **Multi-streaming:** within one association, N streams are each ordered independently. Loss in
  stream 3 doesn't stall stream 1 — killing head-of-line blocking.
- **4-way handshake** with a **cookie** (INIT → INIT-ACK+cookie → COOKIE-ECHO → COOKIE-ACK) →
  resists SYN-flood DoS.

---

## 3. Why 5G / telecom uses it

- Signalling must not drop when one link fails — **multi-homing** gives carrier-grade resilience.
- Many independent signalling transactions run concurrently — **multi-streaming** keeps them
  isolated.
- It's the transport under **SS7/SIGTRAN** and various 3GPP interfaces — which is why it shows up
  across the mobility stack you work on.

---

## 4. SCTP in Go (your contribution)

- The de-facto Go library is **[`ishidawataru/sctp`](https://github.com/ishidawataru/sctp)** (152★).
- You added **`SyscallConn()`** to `SCTPConn` and `SCTPListener` so they expose `syscall.RawConn`
  — matching the stdlib `net.Conn` / `net.Listener` contract. This lets callers set low-level
  socket options (like **DSCP marking**, Lesson 06) and integrate with stdlib-style code.
- You also built **unit-test infra** for SCTP servers that previously hung on a blocking
  `Listen()`, and implemented **DSCP marking for SCTP** end-to-end.

This is a strong, specific story: a real upstream merge on a widely used networking library.

---

## 5. SCTP vs TCP vs UDP — one table

| | TCP | UDP | SCTP |
|---|---|---|---|
| Reliable | ✅ | ❌ | ✅ |
| Ordered | ✅ (one stream) | ❌ | ✅ (per stream) |
| Multi-path | ❌ | ❌ | ✅ |
| Message boundaries | ❌ | ✅ | ✅ |
| Handshake | 3-way | none | 4-way (cookie) |

---

## 🎤 Say this in the interview

- *"SCTP is reliable like TCP but multi-homed and multi-streamed: an association spans multiple
  IPs for failover, and independent streams remove head-of-line blocking — which is why telecom
  and 5G signalling use it."*
- *"It's message-oriented, so it preserves boundaries, and its 4-way cookie handshake resists
  SYN-flood DoS."*
- *"I contributed `SyscallConn()` upstream to `ishidawataru/sctp` so SCTP sockets match the stdlib
  `net.Conn` contract — which is what let us set DSCP socket options on SCTP."*

➡️ **Next:** [04 — Routing & BGP](./04_Routing_And_BGP.md)
