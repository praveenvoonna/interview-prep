# 05 — Errors, Deadlines & Cancellation

## 🧠 The One Idea

**Every gRPC call carries a stopwatch and a kill-switch: a deadline says "give up after this long,"
and cancellation propagates "never mind" all the way down the call chain.** Combined with typed
status codes (not vague strings), this is what keeps a distributed system from hanging forever or
piling up doomed work.

The one-liner: **"gRPC errors are typed status codes; every call should carry a deadline, and
cancellation propagates through context so nobody does work for a caller that already gave up."**

---

## 1. Status codes (typed errors)

- gRPC returns a **status code** + message, not an HTTP 500 blob.
- Common codes: `OK`, `INVALID_ARGUMENT`, `NOT_FOUND`, `ALREADY_EXISTS`, `PERMISSION_DENIED`,
  `UNAUTHENTICATED`, `DEADLINE_EXCEEDED`, `UNAVAILABLE`, `INTERNAL`, `RESOURCE_EXHAUSTED`.

```go
return nil, status.Errorf(codes.NotFound, "user %s not found", id)
// client: st, _ := status.FromError(err); st.Code()
```

Codes let callers react programmatically (retry `UNAVAILABLE`, don't retry `INVALID_ARGUMENT`).

---

## 2. Deadlines & timeouts

- A **deadline** is an absolute "stop by" time the client sets and gRPC **sends to the server**.
- If exceeded, both sides get `DEADLINE_EXCEEDED` and stop.

```go
ctx, cancel := context.WithTimeout(ctx, 2*time.Second)
defer cancel()
resp, err := client.GetUser(ctx, req)
```

**Always set a deadline** — a missing deadline means a stuck server can hang the client forever.

---

## 3. Deadline propagation

- The deadline travels **with the call**, so a chain A→B→C all share the remaining budget.
- Downstream services see the same deadline and stop when it passes — no wasted work.
- This is the distributed version of "the whole request gives up together".

---

## 4. Cancellation

- If the client cancels (or disconnects), the server's `ctx.Done()` fires — your handler should
  **check `ctx.Err()`** and stop work (DB queries, loops, downstream calls).
- Prevents "zombie" work for a caller who already left.

```go
select {
case <-ctx.Done(): return ctx.Err()
default: // keep working
}
```

---

## 5. Retries & idempotency

- Safe to retry **`UNAVAILABLE`/`DEADLINE_EXCEEDED`** (transient) — with **backoff + jitter**.
- **Don't** retry `INVALID_ARGUMENT`/`PERMISSION_DENIED` (deterministic failures).
- Only retry **idempotent** operations, or use an idempotency key, or you'll double-apply.
- gRPC supports a built-in **retry policy** via service config.

---

## 🎤 Say this in the interview

- *"gRPC errors are typed status codes, so callers branch on them — retry UNAVAILABLE, never retry
  INVALID_ARGUMENT."*
- *"I set a deadline on every call; it propagates down the chain so the whole request gives up
  together and nobody does work past the budget."*
- *"Handlers honor cancellation via ctx.Done(), and I only retry idempotent, transient failures with
  backoff and jitter."*

➡️ **Next:** [06 — Hands-on lab (Go)](./06_Hands_On_Lab.md)
