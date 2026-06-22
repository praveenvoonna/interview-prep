# 02 — Transactions & ACID

## 🧠 The One Idea

**A transaction is "all or nothing": like a bank transfer that must either move the money
completely or not at all — never leave it half-done.** ACID is the set of promises a database makes
so that, even with crashes and many users at once, your data never ends up in a broken in-between
state.

The one-liner: **"ACID = a transaction is atomic, leaves the DB consistent, runs isolated from
others, and survives crashes once committed."**

---

## 1. ACID, defined

- **Atomicity:** all statements succeed or none do (rollback on failure).
- **Consistency:** a committed transaction moves the DB from one valid state to another (respects
  constraints).
- **Isolation:** concurrent transactions don't step on each other; results look serial.
- **Durability:** once committed, it survives a crash (written to durable storage / WAL).

```sql
BEGIN;
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
  UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;   -- both, or ROLLBACK leaves neither
```

---

## 2. The concurrency anomalies

Without isolation, these bugs appear:

- **Dirty read:** read another transaction's *uncommitted* change.
- **Non-repeatable read:** re-reading the same row gives a different value (someone committed an
  update in between).
- **Phantom read:** re-running a range query returns new rows (someone inserted).
- **Lost update:** two transactions overwrite each other's change.

---

## 3. Isolation levels (the dial)

From weakest/fastest to strongest/slowest:

| Level | Prevents |
|---|---|
| Read Uncommitted | (nothing — allows dirty reads) |
| Read Committed | dirty reads |
| Repeatable Read | + non-repeatable reads |
| Serializable | + phantoms (behaves as if serial) |

Higher isolation = more correctness, less concurrency. **Read Committed** is the common default
(Postgres); pick **Serializable** only when you must.

---

## 4. How isolation is implemented

- **Locking:** shared (read) / exclusive (write) locks; risk of **deadlocks** (the DB detects and
  kills one).
- **MVCC (Multi-Version Concurrency Control):** readers see a consistent **snapshot** while
  writers create new versions — so **reads don't block writes**. Used by Postgres/MySQL-InnoDB;
  this is why those databases scale reads well.

---

## 5. Optimistic vs pessimistic concurrency

- **Pessimistic:** lock first, assume conflict (good under high contention).
- **Optimistic:** don't lock; check a **version/timestamp** at commit, retry on conflict (good
  under low contention) — common in distributed and NoSQL stores (e.g. DynamoDB conditional
  writes).

---

## 🎤 Say this in the interview

- *"ACID means a transaction is atomic, keeps the DB consistent, runs isolated, and is durable once
  committed — so a transfer never half-completes."*
- *"Isolation levels are a dial against anomalies: Read Committed stops dirty reads, Serializable
  stops phantoms too, trading concurrency for correctness."*
- *"MVCC lets readers see a snapshot so reads don't block writes; for distributed updates I'd use
  optimistic concurrency with a version check and retry."*

➡️ **Next:** [03 — Query plans & optimization](./03_Query_Plans_And_Optimization.md)
