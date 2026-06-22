# 00 — What is RPC & Why gRPC

## 🧠 The One Idea

**RPC lets you call a function on another machine as if it were local — the network is hidden
plumbing.** You write `user, err := client.GetUser(ctx, id)` and it *feels* like a normal function
call, but under the hood the arguments are serialized, sent over the network, executed on a server,
and the result sent back. **gRPC** is Google's modern, fast, strongly-typed take on this, running
over HTTP/2.

The one-liner: **"gRPC is contract-first RPC over HTTP/2 with Protobuf — typed stubs make a remote
call look local."**

---

## 1. What "RPC" means

- **Remote Procedure Call:** invoke a procedure in another address space (another process/host).
- The framework handles **marshalling** (serialize args), transport, dispatch, and
  **unmarshalling** (deserialize result).
- You think in **methods and messages**, not URLs and verbs.

---

## 2. Why gRPC specifically

- **Contract-first:** you define services/messages in a `.proto` file; codegen produces typed
  client + server stubs in many languages.
- **HTTP/2 transport:** multiplexing, streaming, header compression, one long-lived connection.
- **Protobuf:** compact binary serialization (Lesson 01) — smaller and faster than JSON.
- **Polyglot:** the same `.proto` generates Go, Java, Python, etc. — perfect for microservices.

---

## 3. The big picture

```
service.proto  ──protoc──▶  generated stubs (client + server)
                                  │
client code → stub → [HTTP/2 + Protobuf] → server stub → your handler
```

You implement the **server handler** and call the **client stub**; gRPC generates everything in
between.

---

## 4. Where it fits

- **Internal service-to-service** calls at scale — the sweet spot (vs REST for public/browser APIs).
- A common real-world example: the **BGP management plane is gRPC** — e.g. binding the GoBGP gRPC
  server — and gRPC version upgrades ripple across many repos.
- 5G's Service-Based Architecture uses HTTP/2 APIs in the same spirit.

---

## 5. gRPC vs REST in one breath

| | gRPC | REST |
|---|---|---|
| Transport | HTTP/2 | usually HTTP/1.1 |
| Payload | Protobuf (binary) | JSON (text) |
| Contract | `.proto` (strict) | OpenAPI (looser) |
| Streaming | first-class (bidi) | limited |
| Browser | needs grpc-web | native |

(Full comparison in Lesson 03.)

---

## 🎤 Say this in the interview

- *"gRPC is contract-first RPC: I define services and messages in a `.proto`, and codegen gives me
  typed client and server stubs, so a remote call looks like a local function."*
- *"It runs over HTTP/2 with Protobuf — multiplexed, streaming, and binary — which makes it ideal
  for internal, high-throughput, polyglot service calls."*
- *"On my team the BGP control plane exposes a gRPC management API, and I drove a repo-wide gRPC
  version upgrade."*

➡️ **Next:** [01 — Protocol Buffers](./01_Protocol_Buffers.md)
