# 03 — gRPC vs REST

## 🧠 The One Idea

**REST is sending postcards with addresses everyone can read; gRPC is a private, high-speed phone
line between two systems that already agreed on a language.** Neither is "better" — REST wins on
universality and human-readability (great for public/browser APIs), gRPC wins on speed, strict
contracts and streaming (great for internal microservices).

The one-liner: **"REST for public/browser APIs (JSON, ubiquitous); gRPC for internal,
high-throughput, polyglot service-to-service calls."**

---

## 1. The core differences

| | gRPC | REST |
|---|---|---|
| Transport | HTTP/2 | usually HTTP/1.1 |
| Payload | Protobuf (binary) | JSON (text) |
| Contract | `.proto` (strict, codegen) | OpenAPI (looser) |
| Streaming | first-class (4 types) | limited (SSE/websockets) |
| Browser | needs grpc-web proxy | native |
| Human-readable | no | yes |

---

## 2. Why gRPC is faster

- **Binary Protobuf** is smaller and faster to (de)serialize than JSON text.
- **HTTP/2 multiplexing:** many concurrent calls over **one** connection (no head-of-line blocking
  at the HTTP layer; no per-call connection setup).
- **Header compression** (HPACK) and persistent connections cut overhead.

---

## 3. Why REST is still everywhere

- **Universal:** every language, browser, curl, and tool speaks HTTP/JSON.
- **Human-readable** and easy to debug by eye.
- **Cacheable** via standard HTTP caching/CDNs.
- **No codegen step** required to call it.

For a **public API** or anything a browser hits directly, REST is usually the pragmatic choice.

---

## 4. When to pick which

- **gRPC:** internal microservice mesh, low-latency/high-throughput, streaming, strongly-typed
  contracts across many languages (your control-plane and 5G world).
- **REST:** public APIs, browser clients, third-party integrations, simple CRUD where readability
  and tooling matter more than raw speed.
- **Both:** expose gRPC internally and a REST/JSON gateway externally (e.g. grpc-gateway).

---

## 5. The contract angle

- gRPC's `.proto` is the **single source of truth** — codegen keeps client and server in lockstep,
  catching mismatches at compile time.
- REST relies on OpenAPI/Swagger + discipline; drift between docs and reality is common.
- This compile-time safety is a real maintainability win for large, multi-team codebases.

---

## 🎤 Say this in the interview

- *"I use gRPC for internal service-to-service calls — binary Protobuf over multiplexed HTTP/2 is
  faster and the `.proto` gives a compile-time-checked contract across languages."*
- *"I use REST for public and browser-facing APIs — JSON is universal, human-readable, and
  cacheable, with no codegen needed."*
- *"A common pattern is gRPC internally with a REST/JSON gateway at the edge."*

➡️ **Next:** [04 — Interceptors, auth & TLS](./04_Interceptors_Auth_TLS.md)
