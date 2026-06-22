# 09 — Interview Q&A Flashcards

Rapid-fire. Cover the answer, say it aloud, then check. Read twice.

---

## Models & IP

**Q: OSI vs TCP/IP in one line?**
OSI is the 7-layer teaching model; TCP/IP is the 4-layer real stack (Link, Internet, Transport,
Application). Each layer adds a header and trusts the one below.

**Q: What does a subnet mask do?**
Splits an address into network prefix + host. `/24` = 24 network bits, 256 addresses, 254 usable.

**Q: IPv6 vs IPv4 — key differences?**
128-bit vs 32-bit, no NAT, no broadcast, `/64` standard subnet, SLAAC autoconfig. Enables
IPv6-only deployments.

---

## Transport

**Q: TCP vs UDP?**
TCP: connection-oriented, reliable, ordered, flow/congestion control, 3-way handshake. UDP:
connectionless, best-effort, 8-byte header, low latency.

**Q: What's the 5-tuple?**
src IP, src port, dst IP, dst port, protocol — uniquely identifies a connection.

**Q: What is SCTP and why telecom?**
Reliable like TCP but **multi-homed** (failover across IPs) and **multi-streamed** (no
head-of-line blocking), message-oriented, 4-way cookie handshake. Used for 5G/SS7 signalling.

**Q: Your SCTP contribution?**
Added `SyscallConn()` to `ishidawataru/sctp` so sockets expose `syscall.RawConn` (stdlib
contract), enabling DSCP socket options; merged upstream.

---

## Routing & BGP

**Q: What is BGP?**
Path-vector protocol exchanging prefixes + AS-path between autonomous systems over TCP/179;
selects by policy (local-pref, then AS-path length), not shortest distance.

**Q: eBGP vs iBGP?**
eBGP between different ASes; iBGP within one AS.

**Q: Announce vs withdraw, and the danger?**
Announce adds reachability; withdraw removes it. Missing withdrawals cause **blackholes**.

**Q: Soft-reset vs hard-reset?**
Soft-reset re-applies policy and re-evaluates routes **without dropping the session**; hard-reset
tears it down. Use soft-reset to avoid churn.

**Q: What's GoBGP and what did you do with it?**
A Go BGP daemon with a gRPC API. I decoupled its management plane onto a dummy interface, added
IPv6-only bring-up, and a route-add retry framework with static fallback.

---

## BFD

**Q: Why BFD if BGP has keepalives?**
BGP timers are coarse (seconds–minutes). BFD heartbeats every few ms detect failure sub-second and
notify BGP → fast convergence.

**Q: How does BFD declare down?**
Miss N consecutive control packets (detect multiplier) → session down → notify clients.

**Q: RFC 5881 port behaviour you implemented?**
Dest UDP 3784, ephemeral source port (49152–65535); I allocate randomized→sequential on
`EADDRINUSE`, cap retries, and cache to throttle etcd watches.

---

## QoS & Linux

**Q: What is DSCP?**
A 6-bit priority field in the IP header (e.g. EF=46 for real-time); routers map it to per-hop
queuing/drop behaviour. Only matters under congestion.

**Q: How do you set DSCP from code?**
Socket options `IP_TOS` / `IPV6_TCLASS`. I implemented it end-to-end for SCTP.

**Q: What is netlink?**
The user-space↔kernel API for networking config (routes/addrs/links). The control plane installs
routes into the kernel FIB over netlink.

**Q: Dummy interface — why?**
A stable, always-up virtual IP not tied to a NIC; binding the control plane to it survives
physical-link flaps.

**Q: What is a VRF?**
Independent routing tables on one host so overlapping prefixes coexist — multi-tenant/slice
isolation.

---

🎉 **You finished Networking From Scratch.** Re-read the three-sentence summary in the
[README](./README.md). The highest-leverage answers: **BGP route selection + soft-reset**, **BFD
sub-second detection + RFC 5881 ports**, and **netlink/dummy-interface decoupling**.
