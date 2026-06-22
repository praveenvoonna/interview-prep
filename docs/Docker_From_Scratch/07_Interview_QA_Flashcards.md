# 07 — Interview Q&A Flashcards

Rapid-fire. Cover the answer, say it aloud, then check. Read twice.

---

## Containers vs VMs

**Q: Container vs VM?**
A container is an isolated process sharing the host kernel (namespaces + cgroups); a VM virtualizes
hardware and runs a full guest OS.

**Q: What do namespaces and cgroups do?**
Namespaces isolate what a process sees (PID/net/mount/user); cgroups limit what it uses (CPU/mem/IO).

**Q: Stronger isolation than plain containers?**
microVMs (Firecracker, Kata) or gVisor — VM-grade isolation at container speed.

---

## Images & layers

**Q: Image vs container?**
Image = read-only stacked layers; container = image + a thin writable layer.

**Q: Why does Dockerfile order matter?**
Layers are cached top-down; ordering stable→volatile (deps before source) maximizes cache hits.

**Q: Why clean up in the same RUN?**
A file added in an earlier layer ships even if a later layer deletes it.

**Q: Tag vs digest?**
Tag is movable; digest (`@sha256`) is immutable — deploy by digest for reproducibility.

---

## Dockerfile

**Q: CMD vs ENTRYPOINT?**
ENTRYPOINT is the fixed executable; CMD is default, overridable args.

**Q: Why exec form not shell form?**
So signals (SIGTERM) reach the process for graceful shutdown.

**Q: What does `.dockerignore` do?**
Keeps junk out of the build context — faster builds, better caching.

---

## Multistage (the headline)

**Q: What is a multistage build?**
Compile in a fat builder stage, `COPY --from` only the artifact into a minimal runtime stage.

**Q: Size impact for a Go app?**
~800MB single-stage → ~10–15MB on scratch/distroless.

**Q: Why is it more secure?**
No shell, package manager, or compiler in the final image → far fewer CVEs and no pivot tools.

**Q: scratch vs distroless vs alpine?**
scratch = empty (needs static binary); distroless = ~2MB with certs, no shell; alpine = ~5MB with a
shell.

---

## Networking & volumes

**Q: How do containers talk by name?**
User-defined bridge networks give built-in DNS by container/service name.

**Q: EXPOSE vs -p?**
EXPOSE documents; `-p host:container` actually publishes the port.

**Q: Volume vs bind mount?**
Volume = Docker-managed persistent storage (prod state); bind mount = host path (dev live-code).

**Q: What happens to writable-layer data on removal?**
It's lost — that's why state goes in volumes.

---

## Security

**Q: Default user — and the fix?**
Root by default; add a user and `USER app`, drop capabilities, read-only FS, no-new-privileges.

**Q: Where do secrets go?**
Never baked into the image (they persist in layers) — inject at runtime.

**Q: Supply-chain hygiene?**
Scan in CI (Trivy/Grype), patch bases, sign images (cosign), keep an SBOM.

---

🎉 **You finished Docker From Scratch.** The interview headline is **multistage builds**: fat builder
→ minimal distroless runtime, ~800MB → ~10MB, smaller *and* safer. Pair it with layer-cache ordering
and non-root hardening and you cover the whole topic.
