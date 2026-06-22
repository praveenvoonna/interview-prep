# 01 — Protocol Buffers

## 🧠 The One Idea

**Protobuf is a compact, agreed-upon shorthand for data: instead of spelling out field names every
time (like JSON), both sides agree on a numbered template and send only the values.** That makes
messages tiny and fast to parse — and the **field numbers** (not names) are the permanent identity,
which is what lets you evolve the schema without breaking old clients.

The one-liner: **"Protobuf is a binary, schema-defined format where field numbers are the wire
identity — compact and forward/backward compatible."**

---

## 1. A `.proto` definition

```proto
syntax = "proto3";
package user;

message GetUserRequest {
  string id = 1;        // the "1" is the field number = wire identity
}

message User {
  string id    = 1;
  string name  = 2;
  int32  age   = 3;
}

service UserService {
  rpc GetUser(GetUserRequest) returns (User);
}
```

`protoc` compiles this into typed structs + client/server stubs.

---

## 2. Why it's compact and fast

- The wire format encodes **field number + type + value** — **never the field name**.
- Numbers are varint-encoded; small ints take 1 byte. Result: much smaller than JSON, faster to
  parse, less bandwidth.
- Trade-off: it's **not human-readable** (binary) — you need the `.proto` to decode it.

---

## 3. Schema evolution (the killer feature)

Because identity is the **field number**:

- **Add** a field with a new number → old clients ignore it (forward compatible).
- **Remove** a field → never reuse its number; mark it `reserved`.
- **Never change** a field's number or type.
- Unknown fields are preserved/ignored, not errors.

This is why Protobuf is ideal for services that deploy independently — exactly the multi-repo,
multi-team upgrades you've done.

---

## 4. proto3 essentials

- **Scalar types:** `string`, `int32/64`, `bool`, `bytes`, `double`, `enum`.
- **Repeated** = a list: `repeated string tags = 4;`.
- **map<k,v>**, nested messages, and `oneof` (exactly one of several fields set).
- proto3 has **no required**; everything is optional with defaults (use `optional` for explicit
  presence).

---

## 5. Protobuf vs JSON

| | Protobuf | JSON |
|---|---|---|
| Size | small (binary) | larger (text) |
| Speed | fast parse | slower |
| Schema | required `.proto` | optional |
| Readable | no | yes |
| Evolution | by field number | by convention |

Use Protobuf for internal APIs (speed, contracts); JSON for public/browser APIs (readability,
ubiquity).

---

## 🎤 Say this in the interview

- *"Protobuf encodes field number + value, not names, so messages are compact and fast — and the
  field number is the permanent wire identity."*
- *"That's what enables schema evolution: add new fields with new numbers, never reuse or retype
  one, and old clients keep working."*
- *"I reach for Protobuf on internal services for size and a strict contract, and JSON for public
  APIs where readability matters."*

➡️ **Next:** [02 — The four call types](./02_The_Four_Call_Types.md)
