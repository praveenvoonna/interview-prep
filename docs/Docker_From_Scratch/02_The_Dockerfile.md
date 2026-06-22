# 02 — The Dockerfile

## 🧠 The One Idea

**A Dockerfile is a repeatable recipe for an image: each line is a step, and Docker bakes them
top-to-bottom into cached layers.** Master a handful of instructions and the build-cache rules and
you can build small, fast, reproducible images.

The one-liner: **"A Dockerfile is a deterministic recipe; the instruction order is a caching
strategy, not just sequence."**

---

## 1. The core instructions

| Instruction | Does |
|---|---|
| `FROM` | base image to start from |
| `WORKDIR` | set/`cd` into a working dir |
| `COPY` / `ADD` | copy files in (`COPY` preferred; `ADD` only for URLs/tar) |
| `RUN` | run a command **at build time** (creates a layer) |
| `ENV` / `ARG` | env var (runtime) / build-time variable |
| `EXPOSE` | document a port (doesn't publish it) |
| `CMD` / `ENTRYPOINT` | what runs **when the container starts** |

---

## 2. Build context & `.dockerignore`

- The **build context** is the directory you pass to `docker build .` — Docker tars it and sends it
  to the daemon.
- A bloated context (e.g. `.git`, `node_modules`, build output) makes builds slow and can bust the
  cache.
- A **`.dockerignore`** excludes them — same idea as `.gitignore`:

```
.git
node_modules
*.log
bin/
```

---

## 3. CMD vs ENTRYPOINT (the classic gotcha)

- **`ENTRYPOINT`** = the fixed executable.
- **`CMD`** = default arguments (and is overridable on `docker run`).
- Common pattern: `ENTRYPOINT ["myapp"]` + `CMD ["--help"]` → runs `myapp --help`, but
  `docker run img --version` runs `myapp --version`.
- Use **exec form** (`["app","arg"]`) not shell form so signals (SIGTERM) reach your process for
  clean shutdown.

---

## 4. Caching wins (recap, applied)

- Order **stable → volatile** (deps before source — Lesson 01).
- Combine `RUN` steps and clean up in the same layer.
- Pin base image versions (`golang:1.25` not `golang:latest`) for reproducibility.
- Use **build args** for things that vary per build, **env** for runtime config.

---

## 5. Runtime hygiene

- Run as a **non-root user** (`USER app`) — defense in depth (Lesson 05).
- Set a **`HEALTHCHECK`** so the orchestrator knows when the app is actually ready.
- Keep one **main process per container** (it's the PID 1) — let the orchestrator handle the rest.

---

## 🎤 Say this in the interview

- *"A Dockerfile is a deterministic recipe where instruction order is my caching strategy — deps
  early, source late."*
- *"I use a `.dockerignore` to keep the build context lean, and exec-form ENTRYPOINT/CMD so SIGTERM
  reaches the app for graceful shutdown."*
- *"I pin base versions, run as non-root, and add a HEALTHCHECK — small, reproducible, and
  orchestrator-friendly."*

➡️ **Next:** [03 — Multistage builds](./03_Multistage_Builds.md)
