# 00 — What is 5G & the Core

## 🧠 The One Idea

**A mobile network is an airport for data, and it has two halves: the runways (radio) and the
terminal building that handles tickets, security, and gates (the core).** The radio gets your
phone connected over the air; the **core** decides who you are, whether you're allowed, what
speed you get, and where your data goes. 5G rebuilt the *terminal building* — the core — as a
set of small, independent services instead of one giant monolith.

The common one-liner: **"5G separates the radio (RAN) from a cloud-native, service-based core."**

---

## 1. The two big pieces: RAN and Core

- **RAN (Radio Access Network)** — the towers/base stations (a 5G one is a **gNB**) plus your
  **UE** (User Equipment = your phone). This is the *air interface* — getting bits on and off the
  radio.
- **Core network** — the brains behind the towers. It authenticates you, tracks where you are,
  sets up your data sessions, applies policy (speed/quota), and routes your traffic to the
  internet. **This course is about the core.**

Think: RAN = the road and on-ramp; Core = the city's traffic control, tolling, and routing.

---

## 2. Control plane vs user plane (the most important split)

The core is split into two planes — memorize this:

- **Control plane** = the **signalling/paperwork**. "Who is this SIM? Are they allowed? What
  policy applies? Set up a session." It handles *decisions*, not your actual data. NFs like
  **AMF, SMF, AUSF, UDM, PCF** live here.
- **User plane** = the **actual data pipe**. Your video, web, and game packets flow through it.
  In 5G this is the **UPF**.

**Why split them?** So you can **scale them independently**: data volume explodes → add more
**UPF** capacity near the user; signalling load grows → scale the control NFs. This is called
**CUPS** (Control and User Plane Separation).

---

## 3. Why a *new* core for 5G?

- **More than phones** — billions of IoT devices, cars, factories. One-size networking doesn't
  fit, so 5G adds **network slicing** (logical sub-networks per use case).
- **Cloud-native** — the 4G core was relatively monolithic; the **5G core (5GC)** is designed as
  **microservices** that run on Kubernetes, scale elastically, and upgrade independently.
- **Lower latency & edge** — by separating the user plane, you can push **UPF to the edge** (near
  the user) for ultra-low latency (AR/VR, autonomous systems).
- **API-first** — NFs talk over standard **HTTP/2 REST-style APIs**, so the network behaves like
  a modern software platform.

---

## 4. The three headline 5G use cases (name them)

- **eMBB** — enhanced Mobile Broadband (fast video/browsing).
- **URLLC** — Ultra-Reliable Low-Latency Communications (cars, remote surgery, industrial).
- **mMTC** — massive Machine-Type Communications (huge numbers of IoT sensors).

Slicing is what lets one physical network serve all three with different guarantees.

---


## 🎤 Say this in the interview

- *"A mobile network is RAN (radio) + core. The 5G core is **cloud-native microservices** and
  splits **control plane** (signalling/decisions) from **user plane** (the data pipe, the UPF)
  so they scale independently — that's CUPS."*
- *"5G's new core exists for **slicing, edge/low-latency, and elastic scale**, with NFs talking
  over **HTTP/2 service-based APIs**."*


➡️ **Next:** [01 — Architecture big picture (SBA)](./01_Architecture_Big_Picture.md)
