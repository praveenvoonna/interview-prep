# 10 — Generics, Stdlib & Tooling

## 🧠 The One Idea

**Go gives you just enough abstraction (generics, since 1.18) and a "batteries-included"
standard library + one unified tool, so you build real things fast without pulling in a hundred
dependencies.** Where other ecosystems make you assemble a compiler + formatter + test runner +
package manager + a pile of libraries, Go ships most of it in the box. *Analogy: Go is a
**well-stocked workshop** — the common tools and materials are already on the wall; you rarely
have to drive to the store (npm/pip) for the basics.*

---

## 1. Generics — type-safe reusable code (Go 1.18+)

**The problem they solve:** before generics, to write one function for many types you either
duplicated code per type or used `interface{}` (no type safety, runtime assertions). Generics let
you write it **once, type-safely**.

```go
// T can be any type; works for []int, []string, []User, ...
func Map[T, U any](in []T, f func(T) U) []U {
    out := make([]U, len(in))
    for i, v := range in {
        out[i] = f(v)
    }
    return out
}

doubled := Map([]int{1, 2, 3}, func(n int) int { return n * 2 })  // [2 4 6]
```

- **Type parameters** go in `[...]` after the name. **Constraints** say what the type must
  support:
  - **`any`** = any type; **`comparable`** = supports `==`/`!=` (usable as map keys);
  - a custom **constraint interface** lists allowed types or required methods (e.g.,
    `type Number interface { ~int | ~float64 }` for a generic `Sum`).
- **Use them for** generic containers and algorithms (Map/Filter/Reduce, a typed `Set`, min/max,
  a generic cache). **Don't over-use them** — if an interface or a plain function is clearer,
  prefer that. Idiomatic Go is still mostly non-generic.
- **vs interfaces:** an **interface** says "any type with these *methods*" (dynamic dispatch); a
  **generic** says "this exact code, specialized for whatever *type* you pass" (compile-time,
  type-safe). Different tools.

---

## 2. The standard library — batteries included

A huge amount of real work needs **no third-party packages**. Headliners:

- **`net/http`** — a **production-grade HTTP client *and* server** in the stdlib. You can write a
  real web server in ~10 lines, no framework required. (This is a big reason Go owns backend
  services.)
- **`encoding/json`** — marshal/unmarshal JSON to/from structs via field tags
  (`json:"name"`); also `encoding/xml`, `encoding/base64`, etc.
- **`io`** — the tiny `io.Reader`/`io.Writer` interfaces that **everything** composes around
  (files, network, buffers all look the same).
- **`context`** — cancellation/deadlines (Lesson 07).
- **`sync` / `sync/atomic`** — concurrency primitives (Lesson 07).
- **`testing`** — unit tests, benchmarks, fuzzing — built in (next section).
- **`time`, `os`, `fmt`, `strings`, `strconv`, `sort`, `bufio`, `regexp`, `crypto/*`, `log/slog`
  (structured logging)** — the everyday toolbox.

*Interview line:* *"Go's stdlib is so complete that many services ship with **near-zero external
dependencies** — fewer supply-chain risks and smaller binaries."*

---

## 3. Testing is built in (no framework to choose)

Put `_test.go` files next to your code and run `go test`:

```go
// math_test.go
func TestAdd(t *testing.T) {
    if got := Add(2, 3); got != 5 {
        t.Errorf("Add(2,3) = %d, want 5", got)
    }
}
```

- **Table-driven tests** are the idiom — loop over a slice of `{input, want}` cases, often with
  `t.Run(name, ...)` subtests.
- **Benchmarks:** `func BenchmarkX(b *testing.B)` + `go test -bench=. -benchmem` (Lesson 11).
- **Fuzzing** (`func FuzzX`) and the **race detector** (`go test -race`) come free.
- No external test runner, no assertion library required (though some teams add `testify`).

---

## 4. The `go` tool — one CLI for everything

| Command | Purpose |
|---|---|
| `go run` / `go build` | run / produce the static binary |
| `go test [-race -bench -cover]` | tests, benchmarks, coverage |
| `go fmt` (gofmt) | auto-format — the one true style |
| `go vet` | static checks for suspicious code |
| `go mod init/tidy` / `go get` | module & dependency management |
| `go doc` | read docs from the terminal |
| `go tool pprof` | CPU/memory profiling |

- **`gofmt`** ends all style arguments — every Go file looks the same, so you read anyone's code
  instantly.
- **`go vet`** catches real bugs (bad `Printf` verbs, lock-by-value, unreachable code). Teams
  layer on **`staticcheck`/golangci-lint** for more.
- **Profiling with `pprof`** turns "it feels slow" into a flame graph of exactly where CPU/allocs
  go — *measure, then optimize*.

---

## 5. Why this matters for the role
Kubernetes/OpenShift/OCM code is "just Go": `net/http` servers, `context`-driven reconciles,
table-driven tests with `-race`, `go vet`/lint in CI, and increasingly generics in
controller-runtime helpers. Being fluent with the **stdlib + tooling** is as important as the
language itself — it's the day-to-day of working in that codebase.

---

## 🎤 Say this in the interview

- *"**Generics** (1.18+) let me write type-safe reusable code (containers, Map/Filter) instead of
  `interface{}` + assertions — but I use them judiciously; idiomatic Go is still mostly plain
  functions and small interfaces."*
- *"Go is **batteries-included**: a production `net/http` server, `encoding/json`, `io`,
  `context`, and **built-in `testing`** (table-driven tests, benchmarks, fuzzing, `-race`) mean
  real services with near-zero external deps."*
- *"One **`go` tool** does build/test/format/vet/deps/profile. **`gofmt`** standardizes style,
  **`go vet`/lint** catch bugs in CI, and **`pprof`** lets me profile before optimizing."*

➡️ **Next:** [11 — Hands-on lab](./11_Hands_On_Lab.md)
