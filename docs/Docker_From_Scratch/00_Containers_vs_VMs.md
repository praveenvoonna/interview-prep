# 00 — Containers vs VMs

## 🧠 The One Idea

**A VM is a whole house with its own foundation (a full OS); a container is an apartment in a shared
building (it shares the host kernel).** Both isolate, but a container is really *just a normal
process* on the host with a restricted view — so it starts in milliseconds and weighs megabytes, not
gigabytes.

The one-liner: **"A container is an isolated process sharing the host kernel; a VM virtualizes
hardware and runs a full guest OS."**

---

## 1. The fundamental difference

| | Container | VM |
|---|---|---|
| Isolates | a **process** | **hardware** |
| Kernel | **shares host's** | own guest kernel |
| Size | MBs | GBs |
| Start | milliseconds | seconds–minutes |
| Overhead | near-zero | hypervisor tax |

A VM runs a hypervisor → guest OS → app. A container runs app directly on the host kernel.

---

## 2. The two Linux primitives that make it work

Containers aren't magic — they're two kernel features:

- **Namespaces** = *what a process can see*. Separate views of PIDs, network, mounts, users,
  hostname. The container thinks it has its own machine.
- **cgroups (control groups)** = *what a process can use*. Limit/account CPU, memory, I/O so one
  container can't starve the host.

**Namespaces isolate, cgroups limit.** That's the whole trick.

---

## 3. Why this matters in practice

- **Density:** many containers per host (no duplicated OS) → cheaper, denser than VMs.
- **Speed:** instant start enables autoscaling and fast CI.
- **Consistency:** the image bundles the app *and* its dependencies → "works on my machine" goes
  away.

---

## 4. The trade-off: isolation strength

- Sharing the kernel means **weaker isolation** than a VM — a kernel exploit can cross containers.
- For hostile multi-tenant workloads, people add a layer back: **gVisor**, **Kata Containers**, or
  **Firecracker microVMs** (VM-grade isolation, container-grade speed).
- For trusted internal services (your world), plain containers are the right call.

---

## 5. Where Docker fits

- **Docker** is tooling that makes containers easy: build images, run containers, share via
  registries.
- Under it sits a **container runtime** (containerd → runc) that actually talks to namespaces/cgroups.
- Kubernetes orchestrates these runtimes across many hosts (see the K8s course).

---

## 🎤 Say this in the interview

- *"A container is just an isolated process sharing the host kernel — namespaces give it its own
  view, cgroups cap its resources — whereas a VM virtualizes hardware and runs a full guest OS."*
- *"That's why containers are MBs and start in milliseconds, giving density and consistency, at the
  cost of weaker isolation than a VM."*
- *"For hostile multi-tenant workloads I'd reach for microVMs like Firecracker or gVisor; for trusted
  internal services plain containers are fine."*

➡️ **Next:** [01 — Images & layers](./01_Images_And_Layers.md)
