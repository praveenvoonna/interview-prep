# 02 — Values, Types, Structs & Pointers

## 🧠 The One Idea

**In Go, by default you pass *copies* of things, and a pointer is just "the address of the
original" so you can change it instead of a copy.** Once you internalize "**Go copies values
unless you use a pointer**," a huge class of bugs and confusions disappears. *Analogy: passing a
value is **handing someone a photocopy** of a document — they can scribble on it without
touching your original. Passing a pointer is **handing them the address of your house** — now
they can rearrange your actual furniture.*

---

## 1. Everything has a type, and a zero value

- Go is **statically typed**: every variable has a type known at compile time. The compiler
  catches type errors before you run.
- **No uninitialized garbage:** every variable you declare gets a **zero value** automatically —
  `0` for numbers, `""` for strings, `false` for bools, **`nil`** for pointers/slices/maps/
  channels/interfaces/functions. *You never read random memory like in C.*
- Declaring variables:
  ```go
  var x int          // x == 0 (zero value)
  name := "ada"      // := infers type (string) — only inside functions
  const Pi = 3.14159 // compile-time constant
  ```

---

## 2. Value types vs reference-ish types

This distinction explains most "why didn't my change stick?" moments.

- **Value types** — assigning or passing them **copies the whole thing**: numbers, bools,
  strings, **arrays**, and **structs**. Modifying the copy doesn't affect the original.
- **Types that hold a reference internally** — copying the variable copies a small *header* that
  points at shared underlying data: **slices, maps, channels, pointers, functions**. Two copies
  can see the same underlying data. (We dissect these in Lesson 03.)

> Go is **always pass-by-value** — even a pointer is passed by value (you copy the *address*).
> The trick is that copying an address still lets you reach the *same* original.

---

## 3. Structs — your own data types

A **struct** groups related fields. It's a **value type** (copied on assignment/pass).

```go
type User struct {
    Name string      // exported (capitalized) = visible outside the package
    age  int         // unexported (lowercase) = package-private
}

u := User{Name: "Ada", age: 36}
v := u               // v is a full COPY of u
v.age = 99           // u.age is still 36 — they're independent
```

- Go has **no classes/inheritance**. You attach behavior with **methods** (Lesson 04) and reuse
  via **embedding** (composition), not class hierarchies.
- Structs are laid out contiguously in memory (cache-friendly, fast).

---

## 4. Pointers — the address of a value

- A **pointer** holds the **memory address** of a value. `&x` takes the address; `*p`
  dereferences (reads/writes the value at that address).
  ```go
  x := 10
  p := &x      // p points to x   (type *int)
  *p = 20      // change x THROUGH the pointer
  // x is now 20
  ```
- **Why use pointers?** Two reasons:
  1. **To mutate the caller's value** (otherwise the function edits a copy).
  2. **To avoid copying big structs** (pass an 8-byte address instead of a 200-byte struct).
- **Go has no pointer arithmetic** (unlike C) — you can't do `p++` to walk memory. This removes
  a whole category of security bugs while keeping the useful part of pointers.
- **`nil` is the zero value of a pointer.** Dereferencing a `nil` pointer **panics** — a common
  bug to guard against.

### The classic example
```go
func addOneVal(n int)  { n++ }      // edits a COPY — caller unaffected
func addOnePtr(n *int) { *n++ }     // edits the ORIGINAL via address

x := 5
addOneVal(x)   // x still 5
addOnePtr(&x)  // x now 6
```

---

## 5. Stack vs heap — where values live (internals)

This is a favorite interview topic and the bridge to the garbage collector (Lesson 08).

- **Stack** — fast, per-goroutine scratch memory for **local variables**; automatically freed
  when the function returns (just move a pointer). *Think: a stack of plates you push/pop.*
- **Heap** — longer-lived memory for things that must **outlive** the function that made them;
  cleaned up later by the **garbage collector**. *Think: a warehouse the GC periodically tidies.*

**Escape analysis (the magic):** *you don't choose* stack vs heap — the **compiler** does, at
build time. It asks "**does this value escape** the function (e.g., is its address returned or
stored somewhere that outlives the call)?"
- **Doesn't escape →** put it on the **stack** (cheap, no GC).
- **Escapes →** put it on the **heap** (GC will manage it).

```go
func noEscape() int   { x := 42; return x }       // x stays on the stack (copied out)
func escapes() *int   { x := 42; return &x }       // &x outlives the call → x goes to heap
```

See it yourself: `go build -gcflags=-m main.go` prints "moved to heap" decisions (Lesson 11).

**Why you care:** heap allocations create GC work. Writing code that lets values stay on the
stack (avoiding needless pointers/escapes) is how Go programmers reduce GC pressure.

---

## 🎤 Say this in the interview

- *"Go is **pass-by-value** — everything is copied unless I use a **pointer**, which passes an
  address so I can mutate the original or avoid copying a big struct. There's **no pointer
  arithmetic**, which kills a class of bugs."*
- *"No classes — I model data with **structs** (value types) and behavior with methods +
  **composition/embedding**, not inheritance."*
- *"The compiler decides **stack vs heap** via **escape analysis**: if a value's address
  escapes the function it goes to the heap (GC-managed), otherwise it stays on the cheap stack.
  I can inspect it with `-gcflags=-m`."*

➡️ **Next:** [03 — Slices, maps & strings (internals)](./03_Slices_Maps_Strings.md)
