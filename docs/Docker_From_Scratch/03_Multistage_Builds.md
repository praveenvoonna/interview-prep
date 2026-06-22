# 03 — Multistage Builds

> *A very common interview topic — know it cold.*

## 🧠 The One Idea

**Build in a fully-equipped workshop, then ship only the finished product in a tiny box.** A
multistage build uses one fat stage with the whole toolchain (compiler, build deps) to produce a
binary, then copies *just that binary* into a minimal final stage. The compiler, source, and build
junk never reach production.

The one-liner: **"Multistage builds compile in a fat builder stage and copy only the artifact into a
minimal runtime stage — small, fast, and a tiny attack surface."**

---

## 1. The problem it solves

A single-stage Go image carries the **entire Go toolchain + source** — often **800MB+** — even
though running the app needs only a ~10MB static binary. That's wasted space, slower pulls, and a
bigger attack surface.

---

## 2. The pattern

```dockerfile
# --- stage 1: builder (fat) ---
FROM golang:1.25 AS builder
WORKDIR /src
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 go build -o /app ./cmd/server

# --- stage 2: runtime (tiny) ---
FROM gcr.io/distroless/static
COPY --from=builder /app /app
USER nonroot
ENTRYPOINT ["/app"]
```

`COPY --from=builder` reaches into the earlier stage and takes **only** the binary. The final image
is the small base + one file.

---

## 3. The runtime base options (smallest → friendliest)

| Base | Size | Notes |
|---|---|---|
| `scratch` | ~0 | empty; needs a **static** binary, no shell, no certs |
| `distroless/static` | ~2MB | no shell/pkg mgr; has CA certs/tzdata; great default |
| `alpine` | ~5MB | tiny but has a shell (musl libc quirks) |
| `debian-slim` | ~30MB | full libc + shell when you need them |

Go's `CGO_ENABLED=0` static binary runs happily on `scratch`/`distroless` → **800MB → ~10–15MB**.

---

## 4. Why it's also *more secure*

- **No shell, no package manager, no compiler** in the final image → far fewer CVEs and nothing for
  an attacker to pivot with.
- Fewer packages = fewer things to patch (ties to your slim-base-image hardening work).
- This is the "distroless" philosophy: ship only what runs.

---

## 5. Handy extras

- Name stages (`AS builder`) and `COPY --from=` by name.
- **Stop at a stage:** `docker build --target builder` (e.g. to run tests in the builder).
- Copy from an **external image**: `COPY --from=golang:1.25 /usr/local/go/...`.
- Combine with cache mounts (`RUN --mount=type=cache`) for even faster rebuilds.

---

## 🎤 Say this in the interview

- *"Multistage builds compile in a fat builder stage, then `COPY --from` only the binary into a
  minimal runtime — for a static Go binary that's 800MB down to ~10MB."*
- *"I target `scratch` or distroless: no shell, package manager, or compiler in the final image, so
  the attack surface and CVE count drop dramatically."*
- *"It's reproducible too — I can `--target` the builder to run tests, and pin base versions for
  both stages."*

➡️ **Next:** [04 — Networking & volumes](./04_Networking_And_Volumes.md)
