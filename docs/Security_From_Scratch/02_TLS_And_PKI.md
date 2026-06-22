# 02 — TLS & PKI

## 🧠 The One Idea

**TLS turns an open postcard into a sealed, tamper-proof envelope addressed to the right
recipient.** It does three things at once: **encrypts** (privacy), **authenticates** the server
(you're really talking to your bank), and ensures **integrity** (nobody altered the message).
Certificates and a chain of trust (PKI) are what make the authentication part work.

The one-liner: **"TLS gives confidentiality, integrity, and server authentication; certificates + a CA
chain (PKI) prove identity, and mTLS authenticates both sides."**

---

## 1. Symmetric vs asymmetric crypto

- **Symmetric:** one shared key encrypts and decrypts — **fast**, but how do both sides get the key?
- **Asymmetric:** a **public/private key pair** — encrypt with public, decrypt with private (and
  sign with private, verify with public). **Slow**, but solves key exchange.
- **TLS uses both:** asymmetric to securely agree on a symmetric **session key**, then symmetric for
  the bulk data. Best of both.

---

## 2. Certificates & PKI

- A **certificate** binds a public key to an identity (a domain), **signed by a CA** (Certificate
  Authority).
- Your browser trusts a set of **root CAs**; a cert chains root → intermediate → server.
- Verifying the chain = trusting the server's identity. This **chain of trust** is **PKI**.

---

## 3. The handshake (modern, TLS 1.3)

1. Client says hello (supported ciphers, a key-share).
2. Server responds with its **certificate** + its key-share.
3. Client **verifies the cert** against trusted CAs (and expiry/hostname).
4. Both derive the same **session key** (Diffie-Hellman) without ever sending it.
5. Encrypted application data flows with the symmetric session key.

TLS 1.3 is faster (1 round trip) and dropped legacy weak ciphers — **prefer 1.3, disable old
versions**.

---

## 4. mTLS — mutual authentication

- Normal TLS authenticates **the server**. **mTLS** adds a **client certificate**, so the server also
  verifies the client.
- Identity becomes the **cert**, not a password — ideal for **service-to-service** auth in a zero-
  trust mesh.
- This is your TLS-posture / control-plane angle (and pairs with the gRPC course's mTLS lesson).

---

## 5. Forward secrecy & good posture

- **Perfect Forward Secrecy (PFS):** ephemeral keys per session → stealing the server's private key
  later **can't** decrypt past traffic.
- **Good posture:** TLS 1.2/1.3 only, strong ciphers, valid non-expired certs, **HSTS**, automated
  renewal (ACME/Let's Encrypt), and rotate keys.
- Audit it: tools like `testssl.sh`/SSL Labs grade your endpoint.

---

## 🎤 Say this in the interview

- *"TLS gives confidentiality, integrity, and server authentication: asymmetric crypto agrees on a
  symmetric session key, then symmetric encrypts the data."*
- *"Trust comes from PKI — a certificate binds a key to an identity, signed by a CA, and the client
  verifies the chain; mTLS adds a client cert so both sides authenticate."*
- *"For posture I require TLS 1.3 with forward secrecy, disable legacy ciphers, automate cert renewal,
  and audit the endpoint."*

➡️ **Next:** [03 — Secure randomness & secrets](./03_Secure_Randomness_And_Secrets.md)
