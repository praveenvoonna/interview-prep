# 01 — Images & Layers

## 🧠 The One Idea

**An image is a stack of read-only layers, like transparent sheets layered on top of each other —
each Dockerfile instruction adds one sheet.** Docker caches each layer, so if nothing above a layer
changed, it's reused instead of rebuilt. **Ordering your Dockerfile so the stable stuff is at the
bottom is the single biggest build-speed lever.**

The one-liner: **"Images are stacked, cached, content-addressed layers; order instructions
stable-to-volatile so the cache survives."**

---

## 1. Image vs container

- **Image:** the read-only template (the layers). Built once, shared.
- **Container:** a running instance = image layers + a thin **writable layer** on top.
- Many containers from one image share the same read-only layers (disk-efficient).

---

## 2. Layers & the union filesystem

- Each instruction (`FROM`, `RUN`, `COPY`…) creates a **layer** — a diff of filesystem changes.
- A **union filesystem** (overlayfs) stacks them into one coherent view.
- Layers are **content-addressed** (hashed): identical layers are stored once and shared across
  images.

---

## 3. The build cache (why order matters)

Docker reuses a cached layer if **that instruction and everything above it** are unchanged. The
classic win:

```dockerfile
# ✅ deps first (change rarely) → cached across code edits
COPY go.mod go.sum ./
RUN go mod download
# code last (changes every commit) → only this layer rebuilds
COPY . .
RUN go build -o app
```

Put **slow, stable** steps early; **fast, volatile** steps (your source) late. Reversing this
re-downloads dependencies on every code change.

---

## 4. Keeping images small

- A `RUN apt-get install ...` and a later `RUN rm ...` in **separate** layers still ship the files
  (the earlier layer keeps them). Clean up **in the same `RUN`**.
- Combine related commands with `&&` to avoid layer bloat.
- Use a **`.dockerignore`** (Lesson 02) so junk never enters the build.
- The real size win is **multistage builds** (Lesson 03).

---

## 5. Tags & digests

- A **tag** (`myapp:1.4`) is a movable human label; `latest` is just a default tag, not "newest".
- A **digest** (`myapp@sha256:...`) pins the exact content — immutable and reproducible.
- For reproducible prod deploys, **pin by digest**, not a floating tag.

---

## 🎤 Say this in the interview

- *"An image is read-only stacked layers via a union filesystem; a container adds a thin writable
  layer on top."*
- *"Layers are content-addressed and cached, so I order the Dockerfile stable-to-volatile — deps
  before source — to maximize cache hits."*
- *"I clean up within the same RUN so deleted files don't linger in an earlier layer, and I pin prod
  images by digest for reproducibility."*

➡️ **Next:** [02 — The Dockerfile](./02_The_Dockerfile.md)
