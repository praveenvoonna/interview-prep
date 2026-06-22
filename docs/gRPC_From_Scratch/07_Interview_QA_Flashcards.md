# 07 — Interview Q&A Flashcards

Rapid-fire. Cover the answer, say it aloud, then check. Read twice.

---

## RPC & gRPC basics

**Q: What is gRPC?**
Contract-first RPC over HTTP/2 using Protobuf; codegen gives typed client/server stubs so a remote
call looks local.

**Q: Why HTTP/2?**
Multiplexing (many calls on one connection), streaming, header compression, persistent connections.

**Q: When gRPC over REST?**
Internal, high-throughput, polyglot service-to-service calls and streaming; REST for public/browser
APIs.

---

## Protobuf

**Q: What is the wire identity of a field?**
The field **number**, not its name — that's what makes the format compact and evolvable.

**Q: How do you evolve a schema safely?**
Add fields with new numbers; never reuse/retype a number; mark removed ones `reserved`; old clients
ignore unknowns.

**Q: Protobuf vs JSON?**
Protobuf is binary, smaller, faster, needs a schema, not human-readable; JSON is text, universal,
readable.

---

## Call types

**Q: The four call types?**
Unary, server-streaming, client-streaming, bidirectional — chosen by which side sends many messages.

**Q: Example for each?**
Unary = GetUser; server-stream = tail logs/feed; client-stream = upload/batch; bidi = chat/control
channel.

---

## Production concerns

**Q: What's an interceptor?**
gRPC middleware (unary + stream, client + server) for auth, logging, tracing, metrics, recovery.

**Q: Where do tokens travel?**
In **metadata** (gRPC's headers), as per-RPC credentials.

**Q: TLS vs mTLS?**
TLS authenticates the server and encrypts; mTLS adds a client cert so both sides prove identity.

---

## Errors, deadlines, cancellation

**Q: How are errors represented?**
Typed status codes (NOT_FOUND, INVALID_ARGUMENT, UNAVAILABLE, DEADLINE_EXCEEDED…), not opaque
strings.

**Q: Why set a deadline?**
So a stuck server can't hang the client; the deadline propagates down the chain so the whole request
gives up together.

**Q: How does cancellation work?**
Client cancel/disconnect fires `ctx.Done()` on the server; the handler stops work to avoid zombie
processing.

**Q: What's safe to retry?**
Transient codes (UNAVAILABLE, DEADLINE_EXCEEDED) on idempotent ops, with backoff + jitter; never
retry INVALID_ARGUMENT/PERMISSION_DENIED.

---

## Workflow

**Q: The end-to-end workflow?**
`.proto` → `protoc` codegen → implement server interface → call typed client.

**Q: Why embed UnimplementedXServer?**
Forward compatibility — new methods don't break the build.

---

🎉 **You finished gRPC From Scratch.** Lead with the three-sentence summary: **contract-first over
HTTP/2, field numbers enable evolution, HTTP/2 enables streaming/multiplexing.** Then be ready to go
deep on **deadlines/cancellation** and **mTLS + interceptors** — that's where senior interviews
probe.
