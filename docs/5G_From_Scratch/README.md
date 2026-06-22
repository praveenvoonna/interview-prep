# 📡 5G Core From Scratch — A Crash Course (in plain English)

A from-zero, **naive-language** tour of the **5G core network**: what AMF, SMF, UPF, and the
other network functions actually do, and how a phone gets online end-to-end.

> **How to read this:** go in order, 00 → 08. Each lesson starts with a plain-English analogy
> (**"The One Idea"**), then the real details, then **"Say this in the interview"** one-liners
> you can repeat almost verbatim. Don't skip the analogies — they're the hooks that make the
> rest stick.


---

## 📂 The lessons (read in order)

### Part A — The mental model
- **[00 — What is 5G & the core](./00_What_Is_5G.md)** — RAN vs core, control plane vs user
  plane, why a new core exists.
- **[01 — Architecture big picture (SBA)](./01_Architecture_Big_Picture.md)** — the
  Service-Based Architecture: NFs as microservices talking over HTTP/2 APIs.

### Part B — The core network functions (the ones interviewers ask about)
- **[02 — AMF: the front door](./02_AMF.md)** — registration, mobility, connection management.
- **[03 — SMF & UPF: sessions & the data pipe](./03_SMF_And_UPF.md)** — who sets up your data
  session and who actually forwards packets.
- **[04 — Subscriber data & auth](./04_Subscriber_Data_And_Auth.md)** — AUSF, UDM, UDR, UDSF:
  who you are and proving it.
- **[05 — Policy, discovery & slicing](./05_Policy_Discovery_Slicing.md)** — PCF, NRF, NSSF,
  NEF.

### Part C — How it all fits together
- **[06 — End-to-end call flow](./06_End_To_End_Call_Flow.md)** — a phone powering on:
  registration → authentication → PDU session → data flowing.


### Part D — Practice
- **[08 — Interview Q&A flashcards](./08_Interview_QA_Flashcards.md)** — rapid-fire questions
  with crisp answers.

---

## 🧠 The whole course in three sentences

1. **5G splits the network into a RAN (radio) and a core**, and the core is split again into a
   **control plane** (signalling — who you are, what you're allowed) and a **user plane** (the
   actual data packets).
2. **The 5G core is microservices:** each job is a **Network Function** (AMF, SMF, UPF, …) that
   exposes HTTP/2 APIs over a **Service-Based Architecture**, discovered via the **NRF**.
3. **The control and user planes are separated (CUPS):** the UPF forwards packets at high
   speed while the control-plane NFs make the decisions, so each scales independently.

If you can say those three sentences and explain each, you've got the backbone.

---

## 🔤 The acronym cheat-sheet (keep this handy)

| NF | Full form | Plain-English job |
|---|---|---|
| **UE** | User Equipment | Your device (the phone). |
| **(R)AN** | (Radio) Access Network | The radio side of the network. |
| **gNB** | Next-generation NodeB | The 5G base station (tower). |
| **AMF** | Access and Mobility Management Function | Front door: registration, mobility, connection management. |
| **SMF** | Session Management Function | Sets up & manages your data **sessions** (PDU sessions). |
| **UPF** | User Plane Function | The actual **data pipe** — forwards user packets to the internet. |
| **AUSF** | Authentication Server Function | Authentication server — proves the SIM is genuine. |
| **UDM** | Unified Data Management | Subscriber data manager (profile, keys, auth material). |
| **UDR** | Unified Data Repository | The database behind the UDM. |
| **UDSF** | Unstructured Data Storage Function | Shared external store for NF runtime/session state. |
| **PCF** | Policy Control Function | Policy & charging rules (speed limits, quotas). |
| **NRF** | Network Repository Function | Service registry — NFs register and discover each other. |
| **NSSF** | Network Slice Selection Function | Network **slice** selection. |
| **NEF** | Network Exposure Function | Safe API exposure to outside applications. |

### Protocols, interfaces & concepts

| Abbr. | Full form | Plain-English meaning |
|---|---|---|
| **5GC** | 5G Core | The 5G core network as a whole. |
| **NF** | Network Function | One microservice in the core (AMF, SMF, …). |
| **SBA** | Service-Based Architecture | NFs as microservices talking over HTTP/2 APIs. |
| **SBI** | Service-Based Interface | An NF's RESTful HTTP/2 API (e.g. Namf, Nsmf). |
| **CUPS** | Control and User Plane Separation | Splitting signalling (control) from data (user) plane. |
| **NAS** | Non-Access Stratum | Direct control signalling between the UE and the core. |
| **PDU** | Protocol Data Unit | A PDU **session** is your logical data connection (gets an IP). |
| **PFCP** | Packet Forwarding Control Protocol | How the SMF programs the UPF (over N4). |
| **5G-AKA** | 5G Authentication and Key Agreement | The challenge–response that authenticates the SIM. |
| **QoS** | Quality of Service | Speed/priority guarantees for traffic. |
| **S-NSSAI** | Single Network Slice Selection Assistance Information | The identifier of a network **slice**. |
| **DN** | Data Network | The network the UPF connects you to (e.g. the internet). |
| **eMBB** | enhanced Mobile Broadband | Fast video/browsing use case. |
| **URLLC** | Ultra-Reliable Low-Latency Communications | Cars, industrial, remote control use case. |
| **mMTC** | massive Machine-Type Communications | Huge numbers of IoT sensors. |
| **IPAM** | IP Address Management | Allocates IPs (incl. UE session IPs). |
| **BFD** | Bidirectional Forwarding Detection | Sub-second failure detection driving failover. |
| **BGP** | Border Gateway Protocol | Advertises service VIPs for NF reachability. |

Start at **[00](./00_What_Is_5G.md)**.
