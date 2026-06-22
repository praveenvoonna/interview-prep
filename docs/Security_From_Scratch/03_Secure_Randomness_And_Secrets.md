# 03 — Secure Randomness & Secrets

## 🧠 The One Idea

**For anything security-sensitive, "random" must be *unpredictable*, not just *statistically spread* —
and secrets must live anywhere except your source code.** Using the wrong random generator or
hardcoding a key are two of the most common, most preventable vulnerabilities.

The one-liner: **"Use `crypto/rand` not `math/rand` for anything security-sensitive, and keep secrets
out of code in a secrets manager."**

---

## 1. PRNG vs CSPRNG (`math/rand` vs `crypto/rand`)

- **`math/rand`** is a **PRNG**: fast, deterministic, **seeded** — great for simulations, **fatal**
  for security. Given some outputs, an attacker can predict the rest.
- **`crypto/rand`** is a **CSPRNG**: unpredictable, drawn from the OS entropy pool.
- **Rule:** tokens, session IDs, keys, nonces, salts, password-reset codes → **always
  `crypto/rand`**.

*(This is exactly your `math/rand` → `crypto/rand` remediation — name it as a concrete fix.)*

---

## 2. The G115 class of bugs (gosec)

- **G115** flags **integer overflow on conversion** (e.g. `int` → `int32`/`uint`), where a large or
  negative value silently wraps.
- Why it's a security issue: a wrapped length/size/index can bypass bounds checks → memory/logic
  bugs, allocation attacks.
- **Fix:** validate ranges before converting; use appropriately sized types; check bounds explicitly.

*(Your gosec G115 fixes are a credible "I harden Go code" story.)*

---

## 3. Hashing passwords (not the same as encryption)

- **Never** store plaintext or reversibly-encrypted passwords.
- Use a **slow, salted password hash**: **bcrypt**, **scrypt**, or **Argon2** — designed to be
  expensive to brute-force.
- **Salt** (unique per user) defeats rainbow tables; the slow cost defeats mass cracking.
- Don't use fast hashes (MD5/SHA-1/SHA-256 alone) for passwords — they're built for speed.

---

## 4. Secrets management

- **Never** commit secrets (API keys, DB passwords, private keys) to source — they live forever in
  Git history.
- Store them in a **secrets manager**: HashiCorp Vault, AWS Secrets Manager, K8s Secrets (encrypted
  at rest).
- **Inject at runtime** via env/mounted files; **rotate** regularly; scope access least-privilege.
- **Scan** for leaked secrets in CI (gitleaks, truffleHog) and pre-commit hooks.

---

## 5. Cryptography hygiene

- **Don't roll your own crypto** — use vetted libraries and standard algorithms.
- Encryption ≠ hashing ≠ encoding: **encrypt** for confidentiality (reversible with a key), **hash**
  for integrity/passwords (one-way), **encode** (base64) is *not* security.
- Use authenticated encryption (**AES-GCM**) so you get integrity too.

---

## 🎤 Say this in the interview

- *"For anything security-sensitive I use `crypto/rand`, not `math/rand` — a CSPRNG is unpredictable,
  a PRNG is just spread out — which is a fix I've actually shipped."*
- *"I've remediated gosec findings like G115 integer-overflow conversions by validating ranges before
  casting, and I hash passwords with bcrypt/Argon2 plus per-user salt — never fast hashes."*
- *"Secrets never go in code — they live in a manager like Vault, injected at runtime, rotated, and
  scanned for in CI; and I never roll my own crypto."*

➡️ **Next:** [04 — Common vulns (OWASP-style)](./04_Common_Vulns_OWASP.md)
