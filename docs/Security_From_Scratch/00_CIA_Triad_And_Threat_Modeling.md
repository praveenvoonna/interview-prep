# 00 — The CIA Triad & Threat Modeling

## 🧠 The One Idea

**Security isn't a feature you add — it's risk you manage by reducing what an attacker can reach and
layering defenses so no single failure is fatal.** The CIA triad names the three things you're
protecting; threat modeling is asking "what could go wrong here?" *before* you build.

The one-liner: **"Security = protecting Confidentiality, Integrity, Availability by shrinking the
attack surface and layering defense in depth."**

---

## 1. The CIA triad

| Property | Means | Broken by |
|---|---|---|
| **Confidentiality** | only the authorized can read it | data leak, eavesdropping |
| **Integrity** | data isn't tampered with | injection, MITM, corruption |
| **Availability** | the service stays up for users | DoS, outage |

Every control maps to one or more of these — encryption (C), checksums/signatures (I), redundancy/
rate-limiting (A).

---

## 2. Attack surface

- The **attack surface** = every place an attacker could interact: open ports, APIs, inputs,
  dependencies, credentials.
- **Reduce it:** fewer ports, smaller images (distroless — Docker course), fewer dependencies, least
  privilege.
- This is the unifying theme of your security story: **minimize what's exposed**.

---

## 3. Defense in depth

- **No single control is enough** — layer them so one failure doesn't mean breach.
- Example: WAF + input validation + parameterized queries + least-privilege DB user + encryption at
  rest. An attacker must beat *all* of them.
- "Castle with moats *and* walls *and* guards", not one big gate.

---

## 4. Threat modeling (STRIDE)

Ask, per component, what can go wrong. **STRIDE** is the checklist:

| Threat | Violates |
|---|---|
| **S**poofing | authentication |
| **T**ampering | integrity |
| **R**epudiation | non-repudiation (logging) |
| **I**nformation disclosure | confidentiality |
| **D**enial of service | availability |
| **E**levation of privilege | authorization |

Do it at design time — fixing a threat on a whiteboard is free; fixing it in prod is a breach.

---

## 5. Core principles

- **Least privilege:** grant the minimum access needed (users, services, tokens, IAM).
- **Fail securely:** on error, deny — don't fall open.
- **Zero trust:** never trust by network location; verify every request (mTLS, authn/authz).
- **Shift left:** find issues early (design, code, CI) where they're cheap to fix.

---

## 🎤 Say this in the interview

- *"I frame security as risk management around the CIA triad — confidentiality, integrity,
  availability — by shrinking the attack surface and layering defense in depth."*
- *"I threat-model at design time with STRIDE so each component's failure modes are handled before
  build, and I default to least privilege and fail-secure."*
- *"It's zero-trust and shift-left: verify every request, and catch issues in design and CI where
  they're cheap."*

➡️ **Next:** [01 — AuthN vs AuthZ](./01_AuthN_vs_AuthZ.md)
