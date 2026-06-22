# 04 — Interceptors, Auth & TLS

## 🧠 The One Idea

**An interceptor is a checkpoint every request passes through before reaching your handler — like
airport security for RPCs.** Instead of repeating auth, logging, and metrics in every method, you
write them once as a wrapper that runs around every call. Combine that with **TLS** (encrypt the
pipe) and **per-RPC credentials** (a token on each call) and you have production-grade gRPC.

The one-liner: **"Interceptors are gRPC middleware; TLS encrypts the channel and mTLS authenticates
both sides; tokens ride in metadata."**

---

## 1. Interceptors (gRPC middleware)

- **Unary interceptor:** wraps one request/response.
- **Stream interceptor:** wraps a streaming call.
- Each exists **server-side** and **client-side**.

```go
func authInterceptor(ctx context.Context, req any, info *grpc.UnaryServerInfo,
    handler grpc.UnaryHandler) (any, error) {
    if err := check(ctx); err != nil {   // run BEFORE the handler
        return nil, err
    }
    return handler(ctx, req)             // call the real method
}
```

Use for: auth, logging, metrics, tracing, panic recovery, rate limiting — cross-cutting concerns.

---

## 2. Metadata (gRPC's headers)

- **Metadata** = key/value pairs sent alongside a call (like HTTP headers).
- Carry auth tokens, request IDs, trace context.

```go
md := metadata.New(map[string]string{"authorization": "Bearer " + token})
ctx = metadata.NewOutgoingContext(ctx, md)
// server: md, _ := metadata.FromIncomingContext(ctx)
```

This is where bearer/JWT tokens travel for per-RPC auth.

---

## 3. TLS — encrypting the channel

- **TLS** encrypts traffic and authenticates the **server** (client verifies the server's cert).
- Configure with `credentials.NewTLS(...)` on the server, and the client dials with the CA so it
  trusts the cert.
- Without TLS (`insecure`) tokens travel in the clear — fine only for local dev.

---

## 4. mTLS — both sides prove identity

- **Mutual TLS:** the **client also presents a cert**, so the server authenticates the client too.
- Common in service meshes and zero-trust internal networks — identity is the cert, not a password.
- This is the strong-auth posture for internal microservices (your control-plane world).

---

## 5. Per-RPC credentials vs channel credentials

- **Channel credentials** (TLS/mTLS) secure the **connection**.
- **Per-RPC (call) credentials** attach a **token to each call** (e.g. OAuth/JWT in metadata) —
  layered *on top of* TLS.
- Pattern: **mTLS for transport identity + a token in metadata for user/service authorization**,
  checked in a server interceptor.

---

## 🎤 Say this in the interview

- *"I put cross-cutting concerns — auth, logging, tracing, metrics — in interceptors, which are
  gRPC's middleware for unary and streaming calls."*
- *"TLS encrypts the channel and authenticates the server; mTLS adds a client cert so both sides
  prove identity — the zero-trust default for internal services."*
- *"Tokens ride in metadata as per-RPC credentials, layered on TLS, and I validate them in a server
  interceptor."*

➡️ **Next:** [05 — Errors, deadlines & cancellation](./05_Errors_Deadlines_Cancellation.md)
