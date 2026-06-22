# 06 — Interview Q&A Flashcards

Rapid-fire. Cover the answer, say it aloud, then check. Read twice.

---

## Foundations

**Q: What is the CIA triad?**
Confidentiality (only authorized read), Integrity (no tampering), Availability (stays up).

**Q: What is defense in depth?**
Layered controls so no single failure means breach.

**Q: What is STRIDE?**
Threat-modeling checklist: Spoofing, Tampering, Repudiation, Info disclosure, DoS, Elevation of
privilege.

**Q: Core principles?**
Least privilege, fail securely (deny on error), zero trust, shift left.

---

## AuthN vs AuthZ

**Q: Difference?**
AuthN proves who you are; AuthZ checks what you can do — in that order.

**Q: Session vs token?**
Session = server-side state, easy revoke; token (JWT) = stateless/signed, scales, harder to revoke.

**Q: JWT pitfalls?**
It's signed not encrypted; reject `alg:none`, verify signature/expiry/issuer/audience, short expiry.

**Q: OAuth2 vs OIDC?**
OAuth2 = delegated authorization; OIDC = authentication layer on top (login).

---

## TLS & PKI

**Q: What does TLS provide?**
Confidentiality, integrity, server authentication.

**Q: Why both symmetric and asymmetric?**
Asymmetric to agree on a symmetric session key, then fast symmetric for the data.

**Q: What is PKI?**
Certificates bind a key to an identity, signed by a CA; the chain of trust proves identity.

**Q: TLS vs mTLS?**
TLS authenticates the server; mTLS adds a client cert so both sides authenticate.

**Q: Forward secrecy?**
Ephemeral per-session keys → stealing the server key later can't decrypt past traffic.

---

## Randomness & secrets

**Q: math/rand vs crypto/rand?**
math/rand is a predictable PRNG (sim only); crypto/rand is a CSPRNG — use it for tokens/keys/nonces.

**Q: What is G115?**
Integer-overflow on conversion (gosec); a wrapped size/index can bypass bounds checks. Validate
before casting.

**Q: How to store passwords?**
Slow salted hash — bcrypt/scrypt/Argon2 — never plaintext or fast hashes.

**Q: Where do secrets live?**
A secrets manager (Vault/AWS), injected at runtime, rotated, scanned for in CI — never in code.

---

## Common vulns

**Q: Stop SQL injection?**
Parameterized queries/prepared statements; never concatenate input; least-privilege DB user.

**Q: #1 OWASP risk and fix?**
Broken access control; enforce authz server-side every request, owner-checked, deny by default.

**Q: XSS / CSRF / SSRF fixes?**
XSS → output-encode + CSP; CSRF → anti-CSRF tokens + SameSite; SSRF → allow-list outbound, block
internal IPs.

---

## Secure SDLC

**Q: What does shift-left mean?**
Catch issues early (design/code/CI) where they're cheap, not in prod.

**Q: SAST vs SCA vs DAST?**
SAST = your source (gosec); SCA = dependencies/CVEs (govulncheck); DAST = the running app (ZAP).

**Q: What is fuzzing?**
Throwing malformed/random input to find crashes and boundary/parsing bugs (Go fuzzing, Defensics).

**Q: What is PSB attestation?**
Evidence a product meets a security baseline — proving posture, not claiming it.

---

🎉 **You finished Security From Scratch.** Three-sentence core: **security is risk management around
CIA with defense in depth**, **most breaches are boring — weak auth, leaked secrets, unpatched deps**,
and **prove posture with attestation, SAST/fuzzing, and TLS audits.** Lead with your real wins:
crypto/rand, gosec G115, 45 PSB attestations, fuzzing, port audits.
