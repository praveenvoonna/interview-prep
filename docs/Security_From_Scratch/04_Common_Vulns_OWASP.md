# 04 — Common Vulns (OWASP-style)

## 🧠 The One Idea

**Most breaches aren't exotic — they're the same handful of mistakes: trusting user input, weak
access control, and leaked secrets.** The OWASP Top 10 is the industry's "these keep getting people"
list. Know how each maps to backend code and you can prevent the vast majority.

The one-liner: **"Most vulns come from trusting input and broken access control; the fixes are
validate/parameterize, enforce authz server-side, and patch dependencies."**

---

## 1. Injection (SQL/command/etc.)

- **Cause:** untrusted input concatenated into a query/command → attacker runs their own.
- **Fix:** **parameterized queries / prepared statements** (never string-concatenate SQL); validate
  and allow-list input; least-privilege DB user.

```go
db.Query("SELECT * FROM users WHERE id = $1", id)   // ✅ parameterized
// "SELECT ... WHERE id = " + id                     // ❌ injectable
```

---

## 2. Broken access control (the #1 risk)

- **Cause:** missing/!flawed authorization — IDOR (changing `/orders/123` to `124`), forced
  browsing, privilege escalation.
- **Fix:** enforce authz **server-side on every request**, deny by default, check the object **owner**
  — never rely on hidden UI or client checks.

---

## 3. Broken auth & session issues

- Weak passwords, no MFA, predictable session IDs, tokens that never expire.
- **Fix:** strong password hashing (Lesson 03), MFA, secure/`HttpOnly`/`SameSite` cookies, short
  token expiry, rotate on privilege change.

---

## 4. The web classics (XSS, CSRF, SSRF)

- **XSS:** attacker's script runs in a victim's browser. **Fix:** output-encode, Content-Security-
  Policy, sanitize.
- **CSRF:** victim's browser is tricked into a state-changing request. **Fix:** anti-CSRF tokens,
  `SameSite` cookies.
- **SSRF:** server tricked into requesting an internal URL. **Fix:** allow-list outbound targets,
  block internal IP ranges (very relevant to cloud metadata endpoints).

---

## 5. The rest of the Top 10 (backend lens)

- **Security misconfiguration:** default creds, verbose errors, open ports → harden, minimize
  (Docker/Networking courses).
- **Vulnerable dependencies:** outdated libs with known CVEs → scan + patch (Lesson 05).
- **Cryptographic failures:** weak/missing encryption, bad randomness → Lessons 02–03.
- **SSRF, logging failures, insecure deserialization** round it out.
- **Sensitive data exposure:** encrypt in transit (TLS) and at rest; don't log secrets.

---

## 🎤 Say this in the interview

- *"Most vulnerabilities trace to trusting input or broken access control — I prevent injection with
  parameterized queries and enforce authorization server-side on every request, owner-checked and
  deny-by-default."*
- *"For the web classics I output-encode against XSS, use anti-CSRF tokens plus SameSite cookies, and
  allow-list outbound calls against SSRF."*
- *"And the boring-but-common ones — misconfiguration and vulnerable dependencies — I handle by
  hardening, minimizing surface, and scanning/patching dependencies in CI."*

➡️ **Next:** [05 — Secure SDLC & scanning](./05_Secure_SDLC_And_Scanning.md)
