# 01 — How a Go Program Runs

## 🧠 The One Idea

**Go turns your source code into one self-contained machine-code file that needs nothing else
to run.** No interpreter, no virtual machine, no "install the runtime first." You `go build`,
get a single binary, copy it anywhere with the same OS/CPU, and it just runs. *Analogy: most
languages ship you a **recipe** that needs a kitchen (Python interpreter / JVM) already
installed at the destination; Go ships you the **finished meal in a sealed container** — open
and eat.* This is the #1 reason Go dominates cloud/containers.

---

## 1. Compiled vs interpreted (the basic distinction)

- **Interpreted (Python, Ruby, JS):** a program (the interpreter) reads your source at runtime
  and executes it line by line. The target machine must have the interpreter installed.
- **Bytecode/VM (Java, C#):** compiled to *bytecode* that runs on a VM (JVM/CLR) — the VM must
  be installed.
- **Compiled to native (Go, C, Rust):** translated **ahead of time** straight into CPU machine
  code. The result runs directly on the OS — nothing extra needed.

Go is in the last group, and goes further: it links **statically** by default.

---

## 2. Static binary — Go's headline trait

- **`go build` produces ONE executable** that includes your code **and the Go runtime** (GC,
  scheduler) and **all your dependencies** baked in. No `.so`/`.dll` to match, no `pip
  install`, no `node_modules`.
- **Why this is huge for containers:** your Dockerfile can be
  ```dockerfile
  FROM scratch          # a literally empty base image
  COPY myapp /myapp
  ENTRYPOINT ["/myapp"]
  ```
  giving a **tiny image (just your binary)** — fast to ship, small attack surface. *This is why
  Go owns cloud-native.*
- **Cross-compilation is trivial:** set `GOOS`/`GOARCH` and build a Linux/ARM binary from your
  Mac in one command: `GOOS=linux GOARCH=arm64 go build`. No cross-toolchain pain.

---

## 3. What's *inside* that binary: the Go runtime

"Compiled, no VM" doesn't mean "no runtime." Linked into every Go binary is a small **runtime**
that manages things for you while the program runs:

- the **goroutine scheduler** (Lesson 05),
- the **garbage collector** (Lesson 08),
- memory allocator, stack growth, channel/`map` machinery, panics.

It's **not a separate VM you install** — it's part of the binary, lightweight, and starts
instantly (unlike a JVM warm-up). *Think "a tiny built-in operating system for your goroutines
and memory," shipped inside the executable.*

---

## 4. The journey from `.go` to running program

```
your .go files
   │  go build / go run
   ▼
[ compiler ] parse → type-check → optimize → generate machine code
   │
   ▼
[ linker ] combine your code + deps + Go runtime → ONE static binary
   │
   ▼
[ OS loads the binary ] → runtime starts → runs main.main() on the main goroutine
```

- **`go run main.go`** = compile to a temp binary and run it (handy for dev).
- **`go build`** = produce the binary you ship.
- Compilation is **fast by design** (seconds), so this loop is tight.

Every program starts at **`package main`** → function **`main()`**. `func init()` functions (if
any) run before `main`, for setup.

---

## 5. Packages & modules (how code is organized & shared)

- **Package** — the unit of code organization; every `.go` file declares one (`package foo`).
  **Capitalized** identifiers are **exported** (public); lowercase are package-private. *That's
  the entire visibility rule — no `public`/`private` keywords.*
- **Module** — the unit of **dependency management**: a tree of packages with a **`go.mod`** file
  declaring the module path and its required dependencies (with versions). `go.sum` records
  checksums for integrity.
- **The `go` tool does it all:** `go mod init`, `go get` (add a dep), `go mod tidy` (sync deps to
  what's actually imported), `go build/test/run`. Dependencies are versioned (semantic import
  versioning) and cached locally — **reproducible builds**, no "works on my machine."

---

## 6. One tool to rule them all (`go`)

Unlike ecosystems that bolt together a compiler + formatter + test runner + package manager,
Go ships **one** CLI:

| Command | What it does |
|---|---|
| `go run` | compile + run (dev) |
| `go build` | produce the static binary |
| `go test` | run tests & benchmarks (testing is built in) |
| `go fmt` | auto-format to the one true style |
| `go vet` | catch suspicious code |
| `go mod ...` | manage dependencies |

This uniformity is a big reason Go teams move fast — *no toolchain bikeshedding.*

---

## 🎤 Say this in the interview

- *"Go compiles **ahead of time to native machine code** and links **statically**, so I get a
  **single self-contained binary** — no interpreter or VM to install."*
- *"That's why Go is the cloud-native default: the binary drops into a `scratch` container for a
  tiny image, and cross-compiling for Linux/ARM is one env var."*
- *"There's still a small **runtime inside the binary** — scheduler + GC — but it's not a
  separate VM, and startup is instant. Modules (`go.mod`) give reproducible, versioned deps, and
  one `go` tool builds/tests/formats everything."*

➡️ **Next:** [02 — Values, types, structs & pointers](./02_Values_Types_Pointers.md)
