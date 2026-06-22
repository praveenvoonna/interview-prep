# 08 — Hands-On Lab

Seeing packets makes the theory stick. These run on Linux/macOS; some need `sudo`.

> **Goal:** observe a TCP handshake, inspect headers, watch routes, and reason about each layer.

---

## Lab 1 — Watch a TCP 3-way handshake (Lesson 02)

```bash
# Terminal 1: capture SYN/SYN-ACK/ACK to a website
sudo tcpdump -i any -n 'tcp port 443 and host example.com'

# Terminal 2: trigger a connection
curl -sI https://example.com >/dev/null
```

Look for the three packets: `[S]` (SYN), `[S.]` (SYN-ACK), `[.]` (ACK), then data. **You just saw
Lesson 02 live.** Note the ephemeral source port the OS chose.

---

## Lab 2 — Inspect layers in one packet (Lesson 00)

```bash
sudo tcpdump -i any -nvv -c 5 'tcp port 443'
```

In the verbose output identify: **IP header** (src/dst, TTL, the **ToS/DSCP** byte), then the
**TCP header** (ports, flags, seq/ack, window). That's L3 then L4 encapsulation in one frame.

---

## Lab 3 — DSCP marking (Lesson 06)

```bash
# Send pings marked EF (DSCP 46 = ToS 0xb8) and capture the ToS byte
sudo tcpdump -i any -nv 'icmp' &
ping -Q 0xb8 -c 3 1.1.1.1     # Linux: -Q sets ToS; macOS uses -k/-z
```

Confirm the captured packets show the **ToS/DSCP** value you set — proving marking happens in the
IP header.

---

## Lab 4 — Routing table & a dummy interface (Lesson 07, Linux)

```bash
ip route show                       # the kernel FIB
ip rule show                        # policy routing rules

# Create a dummy interface with a stable IP (like the BGP bind fix)
sudo ip link add dummy0 type dummy
sudo ip addr add 10.99.0.1/32 dev dummy0
sudo ip link set dummy0 up
ip addr show dummy0                 # always up, not tied to a NIC
sudo ip link del dummy0            # cleanup
```

---

## Lab 5 — Trace the path (Lesson 04)

```bash
traceroute -n 1.1.1.1     # each line is an L3 hop / router
mtr -n 1.1.1.1            # live per-hop loss + latency (if installed)
```

Each hop is a router making an independent forwarding decision — the routed internet from
Lesson 04.

---

## Lab 6 — DNS is L7 over UDP (Lessons 00, 02)

```bash
dig +short example.com
sudo tcpdump -i any -n 'udp port 53' &
dig example.com >/dev/null
```

Notice DNS rides **UDP/53** — connectionless, one request/response, no handshake.

---

## ✅ What to take away

- You watched **encapsulation**, a **TCP handshake**, **DSCP** in the IP header, the **kernel
  routing table**, a **dummy interface**, and **per-hop routing** — the whole course, observed.
- Practise narrating each capture in layer terms; that fluency is what interviewers reward.

➡️ **Next:** [09 — Interview Q&A flashcards](./09_Interview_QA_Flashcards.md)
