# 🐳 Docker From Scratch — A Crash Course (in plain English)

A from-zero course on **containers and Docker** — with a deliberate focus on **multistage
builds** (a common interview topic) and the slim, secure base images used in production.

> **How to read this:** go in order, 00 → 07. Each lesson starts with a plain-English analogy
> (**"The One Idea"**), then the real mechanics, then **"Say this in the interview"** lines.

> **Status:** 📝 *Syllabus ready — lessons to be written.* This README is the outline and the
> reference you can study from today.

---

## 📂 The lessons (planned, read in order)

### Part A — The idea
- **00 — Containers vs VMs** — namespaces & cgroups, why containers are "just processes".
- **01 — Images & layers** — the union filesystem, layer caching, why order matters.

### Part B — Building
- **02 — The Dockerfile** — instructions, build context, `.dockerignore`, caching wins.
- **03 — Multistage builds** — build stage vs runtime stage, tiny final images, static Go
  binaries on `scratch`/`distroless`.

### Part C — Running & shipping
- **04 — Networking & volumes** — bridge/host networks, port mapping, persistent data.
- **05 — Registries & security** — tags, pushing/pulling, image scanning, non-root users,
  reducing the vulnerability surface (ties to your slim Ubuntu base image work).

### Part D — Practice
- **06 — Hands-on lab** — build a multistage Go image, compare sizes, run it.
- **07 — Interview Q&A flashcards** — layers/multistage/security rapid-fire.

---

## 🧠 The whole course in three sentences
1. **A container is an isolated process, not a VM:** namespaces and cgroups give it its own view
   of the system, sharing the host kernel.
2. **Images are stacked, cached layers:** ordering your Dockerfile so stable layers come first is
   the key to fast builds.
3. **Multistage builds give you a tiny, secure runtime image:** compile in a fat builder stage,
   copy only the binary into a minimal final stage.

---

## 🔗 Companion material
- **[Kubernetes From Scratch](../Kubernetes_From_Scratch/)** — what runs your containers in prod.
- **[Go From Scratch](../Golang_From_Scratch/)** — static binaries make the best container payload.

When the lessons are written, start at **00**.
