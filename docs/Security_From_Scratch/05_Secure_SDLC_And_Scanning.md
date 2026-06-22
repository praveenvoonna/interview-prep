# 05 — Secure SDLC & Scanning

## 🧠 The One Idea

**Security works best baked into the pipeline, not bolted on at the end — catch issues in code review
and CI where they're cheap, and *prove* your posture with evidence.** This is where your real story
lives: gosec, fuzzing, port audits, and 45 PSB attestations.

The one-liner: **"Shift security left: SAST, dependency and secret scanning, and fuzzing in CI — then
attest the result so the posture is proven, not claimed."**

---

## 1. Shift left

- The earlier a flaw is found, the cheaper to fix: **design < code < CI < prod < breach** (cost
  rises orders of magnitude rightward).
- Bake security gates into every stage of the SDLC, not a pre-release scramble.

---

## 2. The scanning toolbox

| Type | What it checks | Example |
|---|---|---|
| **SAST** | your source code for vuln patterns | **gosec**, Semgrep |
| **SCA** | third-party deps for known CVEs | `govulncheck`, Trivy, Dependabot |
| **Secret scanning** | leaked keys/tokens | gitleaks, truffleHog |
| **DAST** | the running app from outside | OWASP ZAP |
| **Image scanning** | container layers | Trivy, Grype (Docker course) |

Wire these into CI to **fail the build** on criticals.

---

## 3. SAST in your world (gosec)

- **gosec** scans Go for insecure patterns: weak randomness (G404 → use `crypto/rand`), integer
  overflow conversions (**G115**), command injection, hardcoded creds, weak TLS config.
- Fixing these systematically across repos is exactly your hardening work — **concrete, demonstrable
  secure-coding**.

---

## 4. Fuzzing (Defensics & Go fuzzing)

- **Fuzzing** throws **malformed/random inputs** at your code to find crashes, hangs, and edge-case
  bugs no test anticipated.
- **Go's built-in fuzzing** (`go test -fuzz`) for your parsers/handlers; **Defensics** for
  protocol-level robustness (your networking/control-plane fits this perfectly).
- Great at finding the **integer/boundary/parsing** bugs that become CVEs.

---

## 5. Attestation & audits (the "prove it" layer)

- **PSB (Product Security Baseline) attestation:** a checklist of security requirements a product
  must meet and **evidence** it does — your **45 PSB attestations** are proof of disciplined posture.
- **Port audits:** enumerate listening ports, justify or close each → shrink attack surface
  (Networking course).
- **SBOM + signing:** know exactly what's in your build (supply chain) and prove provenance.
- The theme: **demonstrate** security with artifacts, don't just assert it.

---

## 🎤 Say this in the interview

- *"I shift security left — SAST like gosec, dependency scanning with govulncheck/Trivy, secret
  scanning, and fuzzing all in CI, failing the build on critical findings."*
- *"Concretely, I've remediated gosec findings — weak randomness and G115 integer conversions — and
  used fuzzing (Go's and Defensics) to catch parsing and boundary bugs before they ship."*
- *"And I prove posture rather than claim it: 45 PSB attestations, port audits to shrink surface, and
  SBOM/signing for supply-chain integrity."*

➡️ **Next:** [06 — Interview Q&A flashcards](./06_Interview_QA_Flashcards.md)
