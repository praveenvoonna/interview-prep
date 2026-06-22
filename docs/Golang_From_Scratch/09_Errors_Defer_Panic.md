# 09 — Errors, defer, panic & recover

## 🧠 The One Idea

**In Go, errors are ordinary return values you check right where they happen — not invisible
exceptions that fly up the stack.** Most languages *throw* errors and hope someone *catches*
them somewhere above. Go makes the error a normal value you must look at on the spot:
`if err != nil`. It's more typing, but the control flow is **visible and explicit** — you always
see what can fail and how it's handled. *Analogy: exceptions are a **fire alarm** that someone,
somewhere, might respond to; Go errors are a **checklist item** you tick off at each step.*

---

## 1. Errors are values

- `error` is just a built-in **interface with one method**:
  ```go
  type error interface { Error() string }
  ```
  Anything with an `Error() string` method is an error. (See interfaces, Lesson 04.)
- The universal pattern — **return the result and an error, check the error**:
  ```go
  f, err := os.Open("file.txt")
  if err != nil {
      return err            // handle or pass it up — explicitly
  }
  defer f.Close()
  // ... use f ...
  ```
- **Convention:** the error is the **last return value**; a `nil` error means success. *You check
  it immediately* — no silent skipping.

---

## 2. Creating and wrapping errors

- Make simple errors: `errors.New("not found")` or `fmt.Errorf("bad id %d", id)`.
- **Wrap with context as it travels up** using `%w` — this preserves the original for inspection:
  ```go
  if err != nil {
      return fmt.Errorf("loading config: %w", err)   // adds context, keeps the cause
  }
  ```
- **Inspect wrapped errors** (instead of fragile string matching):
  - **`errors.Is(err, ErrNotFound)`** — "is this (anywhere in the chain) that sentinel error?"
  - **`errors.As(err, &target)`** — "extract a specific error *type* from the chain."
  ```go
  if errors.Is(err, os.ErrNotExist) { /* handle missing file */ }
  ```
- **Sentinel errors** (`var ErrNotFound = errors.New("not found")`) let callers compare against a
  known value with `errors.Is`. Wrapping + `Is`/`As` is the modern idiom (Go 1.13+).

---

## 3. `defer` — "do this when the function returns"

`defer` schedules a call to run **when the surrounding function exits** (normal return *or*
panic). It's Go's cleanup tool — the equivalent of `finally`.

```go
func process() error {
    f, err := os.Open("x")
    if err != nil { return err }
    defer f.Close()          // guaranteed to run no matter how we leave
    // ...lots of code with multiple returns...
    return nil               // f.Close() runs right here, automatically
}
```

- **Why it's great:** you put the cleanup **right next to** the acquisition, so you can't forget
  it across many return paths. Same for `mu.Unlock()`, `wg.Done()`, `cancel()`.
- **LIFO order:** multiple defers run **last-registered-first** (like a stack).
- **Args evaluated at `defer` time**, but the **call runs at return time** — a common subtlety:
  ```go
  x := 1
  defer fmt.Println(x)   // prints 1 (x captured now), even if x changes later
  x = 2
  ```
- A deferred closure can read/modify **named return values** (used to set an error on the way
  out).

---

## 4. `panic` & `recover` — Go's "break glass" mechanism

- **`panic`** stops normal execution, runs deferred functions up the stack, and crashes the
  program with a stack trace. It's for **truly unexpected, unrecoverable** situations
  (programmer bugs, impossible states) — **not** for ordinary errors.
- **`recover`** (only meaningful **inside a `defer`**) **stops a panic** and lets the program
  continue — turning a panic back into a normal error.
  ```go
  func safe() (err error) {
      defer func() {
          if r := recover(); r != nil {
              err = fmt.Errorf("recovered: %v", r)   // convert panic → error
          }
      }()
      mightPanic()
      return nil
  }
  ```

### The golden rule (say this)
**Use errors for expected failures (file missing, bad input, network down); use panic only for
the truly exceptional.** *Don't use panic/recover as exceptions for normal control flow.* The one
common legit use of `recover`: a **server's top-level handler** recovering so **one bad request
doesn't crash the whole process** — exactly what controllers/HTTP servers do.

### What panics on their own (know these)
- nil pointer dereference, index out of range, **nil map write**, **send on closed channel**,
  type assertion mismatch (single-return form), integer divide by zero.

---

## 5. Why this design is good for infra code

- **Explicit failure paths** = you can *see* every place an operation can fail and how it's
  handled — crucial in long-lived controllers where silent failures are dangerous.
- **Wrapping** builds a readable error trail ("loading config: opening file: permission denied")
  without losing the typed cause (`errors.Is/As`).
- **Recover at the boundary** keeps a daemon alive: a panic in one reconcile/request is contained
  instead of taking down the whole operator.

---

## 🎤 Say this in the interview

- *"Go treats **errors as values** you check explicitly (`if err != nil`), not exceptions. I
  **wrap** with `%w` to add context while preserving the cause, and inspect with **`errors.Is`/
  `errors.As`** instead of string matching."*
- *"**`defer`** runs cleanup on the way out (LIFO, args evaluated immediately) — I put
  `Close`/`Unlock`/`cancel` right next to acquisition so multiple returns can't leak."*
- *"**panic/recover** is break-glass, not exceptions: errors for expected failures, panic for
  truly unrecoverable bugs. The legit `recover` use is a **top-level handler** so one bad request
  doesn't crash the whole server."*

➡️ **Next:** [10 — Generics, stdlib & tooling](./10_Generics_Stdlib_Tooling.md)
