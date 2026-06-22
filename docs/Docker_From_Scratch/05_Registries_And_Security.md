# 05 — Registries & Security

## 🧠 The One Idea

**A registry is the app-store for images (push to publish, pull to run), and image security is about
shipping the least possible code as the least privileged user.** Every package you don't include is
a CVE you can't have; every privilege you drop is an exploit that goes nowhere.

The one-liner: **"Push/pull images via registries; harden by minimizing the image, running non-root,
scanning, and pinning by digest."**

---

## 1. Registries & the image workflow

```bash
docker build -t myrepo/app:1.4 .
docker login registry.example.com
docker push myrepo/app:1.4
docker pull myrepo/app:1.4
```

- **Registry** = stores images (Docker Hub, ECR, GHCR, Harbor, a private one).
- A **repository** holds the tags of one image; an image is identified by `repo:tag` or
  `repo@sha256:digest`.

---

## 2. Tags vs digests (reproducibility)

- A **tag** is movable — `app:1.4` can be re-pushed to point at new bits.
- A **digest** (`@sha256:…`) is immutable — pins exact content.
- In production, **deploy by digest** so a redeploy can't silently pull different bits.

---

## 3. Shrinking the attack surface

- **Multistage + distroless/scratch** (Lesson 03): no shell, no package manager, no compiler →
  fewer CVEs and no tools for an attacker to pivot with.
- Fewer packages = less to patch. This is the heart of your slim-base-image hardening story.

---

## 4. Run as non-root + least privilege

- Containers run as **root by default** — a container-escape then has host-root reach.
- Add a user and drop to it:

```dockerfile
RUN adduser -D app
USER app
```

- Further: `--read-only` root FS, `--cap-drop ALL` (add back only what's needed),
  `--security-opt no-new-privileges`, never `--privileged` unless truly required.

---

## 5. Scanning & supply chain

- **Image scanning** (Trivy, Grype, Docker Scout, ECR scan) flags known CVEs in your layers/deps —
  wire it into CI to fail on criticals.
- **Don't bake secrets** into images (they persist in layers) — inject via env/secrets at runtime.
- **Sign images** (cosign) and verify provenance/SBOM for supply-chain integrity.
- Keep base images **patched** and rebuilt regularly.

---

## 🎤 Say this in the interview

- *"Images live in registries; I deploy by digest, not a floating tag, so a redeploy is
  reproducible."*
- *"Security is minimize-and-de-privilege: distroless final image, non-root user, dropped
  capabilities, read-only FS, and no baked-in secrets."*
- *"I scan images in CI with something like Trivy and fail on critical CVEs, keep bases patched, and
  sign images for supply-chain integrity."*

➡️ **Next:** [06 — Hands-on lab](./06_Hands_On_Lab.md)
