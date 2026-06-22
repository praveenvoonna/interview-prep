# 03 — Networking: VPC

## 🧠 The One Idea

**A VPC is your own private, walled neighborhood inside AWS, and you decide the streets
(subnets), which houses face the public road, and who's allowed through each door.** Public
subnets can reach the internet; private subnets stay hidden. Security groups are the locks on
each house; route tables are the street signs. You design the network *before* you place
servers in it.

The common one-liner: **"A VPC is your isolated virtual network; subnets, route tables, and
gateways control traffic flow."**

---

## 1. VPC & subnets

- A **VPC** is a logically isolated network you define with a **CIDR block** (e.g.
  `10.0.0.0/16`). It lives in **one Region**, spanning its AZs.
- A **subnet** is a slice of that CIDR (e.g. `10.0.1.0/24`) pinned to **one AZ**. Put subnets in
  **multiple AZs** for HA.
- **Public subnet** = has a route to an **Internet Gateway**. **Private subnet** = no direct
  internet route (your databases/app servers live here).

---

## 2. Gateways & routing

- **Internet Gateway (IGW)** — lets resources in **public** subnets talk to the internet
  (both ways).
- **NAT Gateway** — lets **private** subnet resources make **outbound** internet calls (patches,
  APIs) **without** being reachable inbound. One-way out.
- **Route tables** — rules saying "traffic to X goes via Y." A subnet is public vs private purely
  by **what its route table points to** (IGW vs NAT/none).
- **VPC endpoints** — reach AWS services (e.g. S3) **privately** without going over the internet.

---

## 3. Security groups vs NACLs (a classic interview question)

| | Security Group | Network ACL |
|---|---|---|
| Attached to | an **instance/ENI** | a **subnet** |
| State | **Stateful** (return traffic auto-allowed) | **Stateless** (must allow both directions) |
| Rules | **Allow only** | **Allow and Deny** |
| Evaluation | all rules together | **numbered order**, first match wins |

**Mental model:** security group = the **lock on your front door** (per host, remembers who you
let in); NACL = the **neighborhood gate** (per subnet, checks every car each way). Use security
groups as the primary control; NACLs for coarse subnet-level deny.

---

## 4. A typical secure layout

```
VPC 10.0.0.0/16  (Region, across 2+ AZs)
├── Public subnets   → route to IGW   → ALB / NAT GW live here
└── Private subnets  → route to NAT   → app servers & databases here
```

Users hit the **load balancer** in the public subnet; it forwards to **app servers** in private
subnets; those reach the internet only **outbound** via NAT. Databases never face the internet.

---

## 5. Connecting VPCs & on-prem

- **VPC Peering** — direct 1:1 private link between two VPCs (non-transitive).
- **Transit Gateway** — a hub that connects **many** VPCs and on-prem (scales better than mesh
  peering).
- **VPN** (over internet, encrypted) or **Direct Connect** (dedicated private line) to your data
  center.

---

## 🎤 Say this in the interview

- *"A VPC is my isolated network; **public subnets** route to an **IGW**, **private subnets** use
  a **NAT gateway** for outbound-only — databases stay private."*
- *"**Security groups** are stateful, instance-level, allow-only; **NACLs** are stateless,
  subnet-level, allow+deny and ordered. SGs are my primary control."*
- *"Standard layout: ALB in public subnets, app + DB in private subnets across **≥2 AZs**; connect
  VPCs with peering or a **Transit Gateway**."*

➡️ **Next:** [04 — Compute: EC2, containers & Lambda](./04_Compute.md)
