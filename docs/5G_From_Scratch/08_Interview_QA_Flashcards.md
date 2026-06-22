# 08 — Interview Q&A Flashcards

Rapid-fire. Cover the answer, say it aloud, then check. Read twice.

---

## Fundamentals

**Q: What are the two halves of a mobile network?**
**RAN** (radio — gNB + the UE/phone) and the **core** (authenticates, tracks location, sets up
sessions, applies policy, routes to the internet).

**Q: Control plane vs user plane?**
Control plane = signalling/decisions (AMF, SMF, AUSF, UDM, PCF). User plane = the actual data
pipe (**UPF**). Splitting them (**CUPS**) lets each scale independently.

**Q: Why a new, cloud-native 5G core?**
Network slicing, edge/low-latency, elastic scale, and API-first NFs — built as microservices on
Kubernetes.

---

## Architecture

**Q: What is SBA?**
**Service-Based Architecture** — each Network Function is a microservice exposing an HTTP/2 REST
API (`N<nf>`), and they discover each other via the **NRF**.

**Q: What does the NRF do?**
Service registry/discovery — NFs register and find each other dynamically; no hard-coded peers.

---

## The network functions

**Q: AMF?**
Access & Mobility Management — control-plane **front door**: registration, connection, mobility,
paging, terminates **NAS**. Coordinates auth and delegates sessions; never touches user data.

**Q: SMF?**
Session Management — sets up/manages **PDU sessions**, allocates the UE **IP**, selects and
programs the **UPF**, pulls policy from the **PCF**.

**Q: UPF?**
User Plane Function — the **data pipe**: forwards user packets (N3 from radio, N6 to internet),
enforces QoS, measures usage. Only NF on the data path; can sit at the **edge**.

**Q: How do SMF and UPF talk?**
Over **N4** using **PFCP** — packet forwarding rules. One SMF controls many UPFs (CUPS).

**Q: AUSF / UDM / UDR?**
AUSF authenticates the SIM (5G-AKA, mutual auth, key derivation). UDM = subscriber data **logic**;
UDR = the **database** behind it.

**Q: UDSF?**
Stores NF runtime/session state externally so NF instances stay **stateless** and can fail over —
same idea as our distributed session datastore.

**Q: PCF?**
Policy Control — QoS, charging, quotas. SMF enforces its rules via the UPF; AMF uses it for
access/mobility policy.

**Q: NSSF?**
Network Slice Selection — picks the **slice** (S-NSSAI) and NF set for a UE.

**Q: NEF?**
Network Exposure — safe API gateway exposing network capabilities to external apps.

---

## Call flow

**Q: Walk through a phone powering on.**
**Register** with the AMF (found via NRF) → **authenticate** via AUSF/UDM (5G-AKA, keys) →
**PDU session**: SMF gets policy from PCF, allocates IP, programs a UPF over N4 → **data flows**
UE→gNB→UPF→internet. After setup the control plane is out of the data path.

**Q: Who handles mobility/handover?**
The **AMF** (handover, paging); the **SMF** may add/switch **UPFs** as the UE moves.

---


🎉 **You finished 5G Core From Scratch.** Re-read the three-sentence summary and the acronym
cheat-sheet in the [README](./README.md), then narrate the **end-to-end call flow** out loud —
it carries the most weight.
