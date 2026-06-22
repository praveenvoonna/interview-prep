# 02 — TCP vs UDP

## 🧠 The One Idea

**TCP is a phone call; UDP is a postcard.** A phone call sets up a connection, confirms the other
side is listening, and you both notice if the line drops — but it costs a setup handshake. A
postcard you just drop in the box: cheap and instant, but no guarantee it arrives or arrives in
order. **TCP buys reliability with overhead; UDP buys speed by giving up guarantees.**

The one-liner: **"TCP is reliable, ordered and connection-oriented; UDP is fast, connectionless
and best-effort."**

---

## 1. TCP — the reliable stream

- **Connection-oriented:** a **3-way handshake** sets it up — `SYN` → `SYN-ACK` → `ACK`.
- **Reliable & ordered:** every byte is sequenced; lost segments are **retransmitted**, and the
  receiver reorders by sequence number.
- **Flow control:** the **receive window** stops a fast sender from drowning a slow receiver.
- **Congestion control:** slow-start / AIMD back off when the *network* is congested (loss = signal).
- **Teardown:** `FIN`/`ACK` both ways (and `TIME_WAIT` to absorb stragglers).

Used by HTTP, gRPC, databases — anything that needs every byte.

---

## 2. UDP — the fast datagram

- **Connectionless:** no handshake, no state — just send.
- **Best-effort:** no retransmit, no ordering, no congestion control built in.
- **Tiny header (8 bytes)** vs TCP's 20+ → low latency, low overhead.
- App must handle loss/ordering itself if it cares.

Used by DNS, DHCP, VoIP/video, QUIC (which rebuilds reliability on top of UDP).

---

## 3. Ports — getting to the right process

- Both use **16-bit ports** (0–65535) so one host runs many services.
- **Well-known:** 53 DNS, 80 HTTP, 443 HTTPS, 179 BGP.
- A connection is identified by the **5-tuple**: `src IP, src port, dst IP, dst port, protocol`.
- **Ephemeral ports** — the OS picks a high source port for outbound connections (RFC 6056). This
  matters in Lesson 05: BFD allocates source ports from the RFC 5881 ephemeral range.

---

## 4. The handshake and why it matters

```
Client            Server
  | --- SYN -----> |     "I want to talk, seq=x"
  | <-- SYN-ACK -- |     "OK, seq=y, ack x+1"
  | --- ACK -----> |     "Got it, ack y+1"   → ESTABLISHED
```

- Costs **1 RTT** before any data — why connection reuse / keep-alive matters for latency.
- `EADDRINUSE` on bind means the 5-tuple (or local port) is already taken — your BFD code handles
  this with randomized→sequential port fallback.

---

## 5. Choosing between them

| Need | Pick |
|---|---|
| Every byte, in order (APIs, DB) | **TCP** |
| Lowest latency, tolerate loss (telemetry, voice) | **UDP** |
| Reliability *and* low latency | QUIC (UDP + its own reliability), or **SCTP** (Lesson 03) |

---

## 🎤 Say this in the interview

- *"TCP gives a reliable, ordered, congestion-controlled byte stream after a 3-way handshake; UDP
  is connectionless best-effort with an 8-byte header — you trade guarantees for latency."*
- *"A connection is the 5-tuple; the OS picks an ephemeral source port for outbound flows, which
  is exactly what BFD does within the RFC 5881 range."*
- *"`EADDRINUSE` means the local port/5-tuple is taken — we fall back from randomized to
  sequential port selection to recover."*

➡️ **Next:** [03 — SCTP](./03_SCTP.md)
