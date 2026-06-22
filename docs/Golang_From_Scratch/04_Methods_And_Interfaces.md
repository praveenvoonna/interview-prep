# 04 — Methods & Interfaces (the internals)

## 🧠 The One Idea

**An interface says "I don't care *what* you are, only *what you can do*" — and in Go you don't
declare that you implement it; you just *do*, and the compiler notices.** This "implicit
satisfaction" is Go's superpower for clean, decoupled code. *Analogy: an interface is a **job
posting** ("must be able to `Read`"). Any type that can `Read` is automatically qualified — it
never had to apply or mention the posting. Compare Java, where a class must explicitly say
`implements Reader`.*

---

## 1. Methods — functions attached to a type

A **method** is a function with a **receiver** (the value it's called on):

```go
type Rectangle struct{ W, H float64 }

func (r Rectangle) Area() float64 { return r.W * r.H }   // value receiver

rect := Rectangle{3, 4}
fmt.Println(rect.Area())   // 12
```

### Value receiver vs pointer receiver (important!)
- **Value receiver `(r Rectangle)`** — gets a **copy**; can't modify the original. Fine for
  small, read-only methods.
- **Pointer receiver `(r *Rectangle)`** — gets the **address**; **can modify** the original, and
  avoids copying a big struct.
  ```go
  func (r *Rectangle) Scale(f float64) { r.W *= f; r.H *= f }  // mutates the real value
  ```
- **Rule of thumb:** if the method needs to **mutate** the receiver, or the struct is **large**,
  use a **pointer receiver** — and then use pointer receivers **consistently** across the type.

You can attach methods to **any named type** you define (not just structs) — e.g.,
`type Celsius float64` can have methods.

---

## 2. Interfaces — a set of required methods

An **interface** is just a **list of method signatures**. Any type that has all those methods
**satisfies** the interface — automatically, no keyword.

```go
type Shape interface {
    Area() float64          // anything with Area() float64 IS a Shape
}

func PrintArea(s Shape) { fmt.Println(s.Area()) }

PrintArea(Rectangle{3, 4})   // Rectangle satisfies Shape implicitly — just works
```

- **Implicit (structural) satisfaction:** because there's no `implements`, your type can satisfy
  interfaces it was written *before* — even ones in *other people's* packages. This is what makes
  Go code so composable and testable.
- **Keep interfaces small** — idiomatic Go favors **tiny** interfaces (often **one method**),
  like the stdlib's `io.Reader`/`io.Writer`/`Stringer`. *"The bigger the interface, the weaker
  the abstraction."* Accept interfaces, return structs.

---

## 3. How an interface works under the hood (the key internal)

This is the most-asked Go internals question. An interface value is **two words (a pair)**:

```
interface value = ( type pointer , value pointer )
                    └─ what concrete type is inside?
                                   └─ pointer to the actual data
```

- The **type word** points to a *type descriptor* (the itable — method set + type info): it's
  how Go knows which concrete `Area()` to call at runtime (dynamic dispatch).
- The **value word** points to the concrete data.

So `var s Shape = Rectangle{3,4}` stores **(type=Rectangle, value→{3,4})**. Calling `s.Area()`
looks up Rectangle's `Area` via the type word and runs it on the value word. *That's runtime
polymorphism, implemented as a 2-word struct + a lookup table — no class hierarchy needed.*

---

## 4. The empty interface & generics note

- **`interface{}` (or `any` since 1.18)** has **zero** required methods, so **every** type
  satisfies it — it can hold *anything* (Go's old "I'll take any value"). You get the value back
  out with a **type assertion** or **type switch**:
  ```go
  func describe(x any) {
      switch v := x.(type) {
      case int:    fmt.Println("int", v)
      case string: fmt.Println("string", v)
      default:     fmt.Println("other")
      }
  }
  ```
- A **type assertion** `v, ok := x.(int)` safely extracts the concrete type (`ok==false` if it's
  not that type; the one-return form **panics** on mismatch).
- Since 1.18, **generics** (Lesson 10) often replace `any` for type-safe reusable code — but
  `any` is still everywhere (e.g., `fmt.Println(...any)`).

---

## 5. The `nil` interface trap (a true senior-level gotcha)

Because an interface is a **(type, value) pair**, it's `nil` **only when BOTH are nil.** A
common bug: returning a `nil` *pointer* as an interface — the value word is nil, but the **type
word is set**, so the interface is **not nil**.

```go
type MyErr struct{}
func (e *MyErr) Error() string { return "boom" }

func bad() error {
    var p *MyErr = nil
    return p            // returns interface (type=*MyErr, value=nil) → NOT nil!
}

if bad() != nil {       // TRUE — surprising! the check passes even though "no error"
    fmt.Println("got an error?!")
}
```

**Fix:** return a literal `nil` for "no error," never a typed nil pointer. Knowing *why* (the
two-word representation) is exactly the depth interviewers probe.

---

## 🎤 Say this in the interview

- *"Interfaces are satisfied **implicitly** — a type just needs the methods; no `implements`.
  That decouples code and makes testing/mocking trivial. I keep interfaces **small** and
  'accept interfaces, return structs.'"*
- *"Under the hood an interface value is a **two-word pair: a type descriptor + a pointer to the
  data**; method calls dispatch through the type's itable."*
- *"Pointer vs value receivers: pointer to **mutate** or avoid big copies, used consistently. And
  the **nil-interface trap** — a typed nil pointer in an interface is **not** `nil` because the
  type word is set — so I return plain `nil` for no-error."*

➡️ **Next:** [05 — Goroutines & the GMP scheduler](./05_Goroutines_And_Scheduler.md)
