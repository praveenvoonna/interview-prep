# 🔌 gRPC From Scratch — A Crash Course (in plain English)

A from-zero course on **gRPC** — the default RPC framework for internal service-to-service APIs.
Protobuf, the four call types, and the production concerns (interceptors, TLS, deadlines) that
interviewers dig into.

> **How to read this:** go in order, 00 → 07. Each lesson starts with a plain-English analogy
> (**"The One Idea"**), then the real mechanics, then **"Say this in the interview"** lines.

> **Status:** 📝 *Syllabus ready — lessons to be written.* This README is the outline and the
> reference you can study from today.

---

## 📂 The lessons (planned, read in order)

### Part A — The idea
- **00 — What is RPC & why gRPC** — calling a remote function like a local one, over HTTP/2.
- **01 — Protocol Buffers** — schema, field numbers, wire format, backward/forward compat.

### Part B — The mechanics
- **02 — The four call types** — unary, server-streaming, client-streaming, bidi-streaming.
- **03 — gRPC vs REST** — HTTP/2, binary vs JSON, contracts, when to pick which.

### Part C — Production concerns
- **04 — Interceptors, auth & TLS** — middleware, mTLS, metadata, per-RPC credentials.
- **05 — Errors, deadlines & cancellation** — status codes, context propagation, retries.

### Part D — Practice
- **06 — Hands-on lab (Go)** — define a `.proto`, generate, write a server + client.
- **07 — Interview Q&A flashcards** — protobuf/streaming/deadline rapid-fire.

---

## 🧠 The whole course in three sentences
1. **gRPC is a contract-first RPC framework over HTTP/2:** you define messages and methods in a
   `.proto`, and codegen gives you typed client/server stubs.
2. **Protobuf is compact and evolvable:** field numbers (not names) are the wire identity, so you
   add fields without breaking old clients.
3. **HTTP/2 unlocks streaming and multiplexing:** which is why gRPC fits internal,
   high-throughput, polyglot service-to-service calls better than REST.

---

## 🔗 Companion material
- **[Go From Scratch](../Golang_From_Scratch/)** — the language you'll implement gRPC in.
- **[5G Core From Scratch](../5G_From_Scratch/)** — SBA NFs talk over HTTP/2 + gRPC-style APIs.

When the lessons are written, start at **00**.
