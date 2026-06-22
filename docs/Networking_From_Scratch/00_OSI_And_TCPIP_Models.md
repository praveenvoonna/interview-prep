# 00 — OSI & TCP/IP Models

## 🧠 The One Idea

**Networking is a stack of postal services, each trusting the one below it.** When you post a
parcel you don't think about trucks, planes or sorting machines — you just write an address and
hand it over. Each layer adds its own envelope, does its one job, and hands down; on the far side
each layer peels its envelope and hands up. **Layering = separation of concerns for the wire.**

The one-liner: **"Each layer adds a header, does one job, and trusts the layer below to deliver."**

---

## 1. The two models

- **OSI (7 layers)** — the teaching model: Physical, Data Link, Network, Transport, Session,
  Presentation, Application. Mnemonic: *"Please Do Not Throw Sausage Pizza Away."*
- **TCP/IP (4 layers)** — what the internet actually runs: Link, Internet, Transport,
  Application. Simpler and real.

You map between them constantly, so know both.

| TCP/IP | OSI | Job | Examples |
|---|---|---|---|
| Application | 5–7 | App-level meaning | HTTP, gRPC, DNS |
| Transport | 4 | Process-to-process delivery | TCP, UDP, **SCTP** |
| Internet | 3 | Host-to-host routing | IP, ICMP, **BGP** |
| Link | 1–2 | Same-wire delivery | Ethernet, MAC, ARP |

---

## 2. What each layer actually does

- **Link (L2):** moves frames between two devices on the *same* network using **MAC addresses**.
  ARP maps an IP to a MAC. This is where switches live.
- **Internet (L3):** moves **packets** between *different* networks using **IP addresses**.
  Routers and **BGP** live here — picking the path across the internet.
- **Transport (L4):** delivers data to the right **process** via **ports**, and decides the
  guarantees (reliable TCP, best-effort UDP, multi-homed SCTP).
- **Application (L7):** the protocol your code speaks — HTTP/gRPC/DNS.

---

## 3. Encapsulation — the envelopes

Sending side wraps the data top→down; each layer prepends its header:

```
[ App data ]                         ← L7
[ TCP hdr | App data ]               ← L4  (segment)
[ IP hdr | TCP hdr | App data ]      ← L3  (packet)
[ Eth hdr | IP ... | Eth trailer ]   ← L2  (frame)
```

The receiving side **de-encapsulates** bottom→up, each layer reading and stripping its own
header. Your BGP/SCTP work lives at L3/L4 — you care about IP headers and transport behaviour.

---

## 4. Why layering matters in practice

- **Independence:** you can swap Wi-Fi for Ethernet (L2) without touching HTTP (L7).
- **Debugging:** you isolate a problem to a layer — "ping works (L3) but the app times out (L4/7)".
- **Where tools sit:** `tcpdump` shows L2–L4 headers; `ping`/`traceroute` probe L3; `curl`
  exercises L7.

A classic interview move: when asked "the page won't load", **walk down the layers** — DNS (L7)?
route/reachability (L3, `ping`)? port open (L4)? TLS (L6/7)?

---

## 🎤 Say this in the interview

- *"Layering is separation of concerns: each layer adds a header, does one job, and trusts the
  layer below — so you can change one layer without touching the others."*
- *"L2 is same-wire delivery by MAC, L3 is host-to-host routing by IP, L4 is process-to-process
  delivery by port — that's where TCP/UDP/SCTP choose their guarantees."*
- *"To debug reachability I walk the stack: DNS, then ping for L3, then the port for L4, then TLS
  and the app."*

➡️ **Next:** [01 — IP addressing & subnets](./01_IP_Addressing_And_Subnets.md)
