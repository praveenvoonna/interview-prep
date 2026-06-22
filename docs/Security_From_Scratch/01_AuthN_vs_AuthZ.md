# 01 — AuthN vs AuthZ

## 🧠 The One Idea

**Authentication asks "who are you?"; authorization asks "what are you allowed to do?"** They're a
sequence: prove identity first, then check permissions. Mixing them up — or doing one and skipping the
other — is behind a huge share of breaches.

The one-liner: **"AuthN proves identity, AuthZ checks permissions — always in that order, and AuthZ on
every request, not just at login."**

---

## 1. The distinction

- **Authentication (AuthN):** verify identity — password, token, certificate, MFA.
- **Authorization (AuthZ):** verify permission — "can *this* identity do *this* action on *this*
  resource?"
- AuthN happens once per session; **AuthZ must be checked on every protected action**.

---

## 2. Sessions vs tokens

- **Session (stateful):** server stores session state; client holds a session cookie. Easy to
  revoke; needs server-side store.
- **Token (stateless):** client holds a signed token (**JWT**); server verifies the signature, no
  lookup. Scales well; **harder to revoke** (use short expiry + refresh tokens).

---

## 3. JWT in one breath

- A **JWT** = `header.payload.signature`, base64url-encoded.
- The **signature** (HMAC or RSA) proves it wasn't tampered with — the payload is **signed, not
  encrypted** (don't put secrets in it; it's readable).
- Verify the signature, **expiry**, issuer, and audience on every request.
- Pitfalls: accepting `alg: none`, not checking expiry, long-lived tokens with no revocation.

---

## 4. OAuth2 & OIDC

- **OAuth2:** an **authorization** framework — delegated access via tokens ("let App X act on my
  behalf") without sharing your password.
- **OIDC (OpenID Connect):** an **authentication** layer on top of OAuth2 — adds an **ID token** so
  you actually know *who* the user is ("Sign in with Google").
- Rule: **OAuth2 = authorization/delegation; OIDC = authentication/login.**

---

## 5. Authorization models

- **RBAC (Role-Based):** permissions via roles (admin, editor, viewer) — simple, common.
- **ABAC (Attribute-Based):** policy from attributes (department, time, resource owner) — finer
  grained.
- **Least privilege:** grant the minimum; deny by default.
- Enforce AuthZ **server-side** at the resource — never trust client-side checks (a hidden button
  isn't security).

---

## 🎤 Say this in the interview

- *"Authentication proves who you are, authorization what you can do — in that order, and I enforce
  authorization server-side on every request, not just at login."*
- *"JWTs are stateless and signed — not encrypted — so I verify signature, expiry, issuer and
  audience, reject `alg:none`, and use short expiry with refresh tokens for revocation."*
- *"OAuth2 is delegated authorization; OIDC adds authentication on top; and I model permissions with
  RBAC and least privilege, deny-by-default."*

➡️ **Next:** [02 — TLS & PKI](./02_TLS_And_PKI.md)
