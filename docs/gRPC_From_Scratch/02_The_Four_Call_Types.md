# 02 — The Four Call Types

## 🧠 The One Idea

**HTTP/2 lets data flow like a phone call, not just a letter — so gRPC offers four shapes of
conversation depending on who needs to keep talking.** Sometimes you ask once and get one answer;
sometimes one side needs to stream many messages; sometimes both do. These four patterns cover
every interaction.

The one-liner: **"gRPC has unary, server-streaming, client-streaming, and bidirectional-streaming
calls — pick by which side sends many messages."**

---

## 1. Unary (the 90% case)

One request → one response. Just like a normal function call.

```proto
rpc GetUser(GetUserRequest) returns (User);
```

Use for: standard request/response (fetch, create, update). This is what most APIs need.

---

## 2. Server streaming

One request → a **stream** of responses. The server keeps sending until done.

```proto
rpc ListEvents(Filter) returns (stream Event);
```

Use for: large result sets, live feeds, progress updates, "tail the logs". The client reads in a
loop until the stream ends.

---

## 3. Client streaming

A **stream** of requests → one response. The client sends many, server replies once at the end.

```proto
rpc UploadMetrics(stream MetricPoint) returns (UploadSummary);
```

Use for: uploads, batching, aggregating many inputs into one result.

---

## 4. Bidirectional streaming

**Both** sides stream independently over one connection.

```proto
rpc Chat(stream Message) returns (stream Message);
```

Use for: chat, real-time sync, long-lived control channels where each side pushes when it has
something. HTTP/2 multiplexing makes this efficient on a single connection.

---

## 5. How to choose

| Who sends many? | Call type |
|---|---|
| neither (1↔1) | **unary** |
| server only | **server streaming** |
| client only | **client streaming** |
| both | **bidirectional** |

All four are built on HTTP/2 streams, so they share one connection — no per-call TCP setup cost.

---

## 🎤 Say this in the interview

- *"gRPC has four call types — unary, server-streaming, client-streaming, and bidi — and I choose
  by which side needs to send many messages."*
- *"Unary covers most APIs; server streaming suits large result sets or live feeds; client
  streaming suits uploads/batching; bidi suits real-time control channels."*
- *"They're all HTTP/2 streams on one multiplexed connection, so streaming doesn't pay per-call
  connection setup."*

➡️ **Next:** [03 — gRPC vs REST](./03_gRPC_vs_REST.md)
