# 🔒 Security From Scratch — A Crash Course (in plain English)

A from-zero course on **application & infrastructure security** — built to surface a strong,
underused story of yours: 45 PSB attestations, gosec **G115** fixes, `math/rand` → `crypto/rand`,
TLS posture, port audits and fuzzing.

> **How to read this:** go in order, 00 → 06. Each lesson starts with a plain-English analogy
> (**"The One Idea"**), then the real mechanics, then **"Say this in the interview"** lines.

> **Status:** 📝 *Syllabus ready — lessons to be written.* This README is the outline and the
> reference you can study from today.

---

## 📂 The lessons (planned, read in order)

### Part A — Foundations
- **00 — The CIA triad & threat modeling** — confidentiality/integrity/availability, attack
  surface, defence in depth.
- **01 — AuthN vs AuthZ** — sessions, tokens (JWT/OAuth2), RBAC, least privilege.

### Part B — Crypto you actually use
- **02 — TLS & PKI** — symmetric vs asymmetric, certificates, the handshake, mTLS (your TLS-posture work).
- **03 — Secure randomness & secrets** — why `crypto/rand` not `math/rand`, secret management,
  the G115 integer-conversion class of bugs.

### Part C — Finding & fixing
- **04 — Common vulns (OWASP-style)** — injection, broken auth, the classics; how they map to backend code.
- **05 — Secure SDLC & scanning** — SAST (gosec), fuzzing (Defensics), dependency/port audits,
  attestation (your 45-PSB story).

### Part D — Recall
- **06 — Interview Q&A flashcards** — TLS/authz/secure-coding rapid-fire.

---

## 🧠 The whole course in three sentences
1. **Security is risk management, not a feature:** you reduce attack surface and add defence in
   depth rather than chasing a single silver bullet.
2. **Most breaches are boring:** weak auth, leaked secrets, unpatched deps — which is why
   scanning, least privilege and secret hygiene matter most.
3. **Prove it, don't claim it:** attestation (PSBs), SAST/fuzzing evidence and TLS audits are how
   you *demonstrate* a secure posture.

---

## 🔗 Companion material
- **[Go From Scratch](../Golang_From_Scratch/)** — where gosec/crypto-rand fixes live.
- **[Networking From Scratch](../Networking_From_Scratch/)** — TLS/ports sit on this.

When the lessons are written, start at **00**.
