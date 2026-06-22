# 00 — What is Kubernetes & Why It Exists

## 🧠 The One Idea

**Kubernetes is a robot operations team that keeps your apps running exactly the way you
described — without you babysitting them.** You hand it a wish list ("I want 3 copies of my
web app, always alive"), and it makes that true and *keeps* it true: if a copy dies, it
starts a new one; if a machine dies, it moves the work elsewhere. You stop saying *"do these
steps"* and start saying *"this is what I want"* — that shift is the whole point.

The common one-liner: **"Kubernetes is a container orchestrator."** This lesson unpacks what
each of those words means.

---

## 1. First, what's a container?

- A **container** is your app **plus everything it needs to run** (libraries, runtime, files)
  packed into one portable bundle (a Docker **image**). It runs the same on your laptop, a
  test server, or the cloud — *"it works on my machine" becomes "it works everywhere."*
- Think of it as a **standardized shipping container**: the contents vary, but the box is a
  standard size, so any crane (machine) can lift it.
- A container is **lighter than a virtual machine** — it shares the host's OS kernel instead
  of booting its own OS, so it starts in milliseconds.

**Containers solve packaging.** They do *not* solve running hundreds of containers across
many machines reliably. That's the gap Kubernetes fills.

---

## 2. The problem: running containers *at scale* is hard

Imagine you have 50 containers across 10 machines. Now answer, by hand, 24/7:
- A container crashed — **who restarts it?**
- A whole machine died — **who moves its containers to healthy machines?**
- Traffic tripled — **who starts more copies, then scales back down later?**
- You shipped a new version — **how do you roll it out without downtime, and roll back if
  it's broken?**
- Container A needs to find container B — **what's B's address today** (it changes when B
  restarts)?
- A container needs a password — **how do you give it secrets safely?**

Doing all this manually is impossible at scale. **Kubernetes automates every one of these.**
That's what "orchestration" means: not just running containers, but **scheduling,
self-healing, scaling, networking, rollouts, and config** — the whole operations job.

---

## 3. The core philosophy: declarative & desired state

This is the single most important idea in Kubernetes — interviewers test it constantly.

- **Imperative** = you give step-by-step commands: *"start a container, now start another,
  now restart that crashed one…"* You own every step and every failure.
- **Declarative** = you describe the **end result you want** ("desired state"), and the
  system figures out the steps and keeps it true. *"I want 3 healthy copies"* — forever.

**The thermostat analogy (memorize this):** you set a thermostat to 21°C (desired state). It
constantly checks the room (actual state) and turns heating on/off to close the gap. You
never tell it *"turn on for 4 minutes"* — you just declare the target. **Kubernetes is a
thermostat for your applications.**

This loop — *observe actual → compare to desired → act to close the gap → repeat forever* —
is called the **reconciliation loop** (or control loop), and it's literally how every part of
Kubernetes works. We'll see it again and again.

---

## 4. What you actually do as a user

You write **YAML files** describing objects you want, and apply them:

```bash
kubectl apply -f my-app.yaml   # "here is my desired state, make it real"
```

A tiny example — "I want 3 copies of nginx":

```yaml
apiVersion: apps/v1
kind: Deployment        # the KIND of object
metadata:
  name: web
spec:
  replicas: 3           # desired state: 3 copies
  selector:
    matchLabels: { app: web }
  template:             # the recipe for each copy (a Pod)
    metadata:
      labels: { app: web }
    spec:
      containers:
        - name: nginx
          image: nginx:1.27
```

You apply it once. From then on, Kubernetes **keeps 3 copies alive forever** — kill one and a
new one appears in seconds. You declared *what*, not *how*.

---

## 5. What Kubernetes gives you (the headline features)

- **Self-healing** — restarts failed containers, replaces ones on dead nodes.
- **Scaling** — run N copies; scale up/down by changing one number (or automatically).
- **Rollouts & rollbacks** — ship new versions gradually with zero downtime; undo instantly.
- **Service discovery & load balancing** — apps find each other by **name** (stable DNS),
  traffic spread across copies, even as copies come and go.
- **Config & secrets** — inject configuration and passwords without rebuilding images.
- **Storage orchestration** — attach disks to apps automatically.
- **Bin-packing** — efficiently places containers onto machines based on what they need.

---

## 🎤 Say this in the interview

- *"Containers solve packaging; Kubernetes solves **operating** containers at scale —
  scheduling, self-healing, scaling, rollouts, networking, and config."*
- *"Kubernetes is **declarative**: I describe the desired state and a **control loop**
  continuously reconciles actual state to match it — it's a thermostat for my apps."*
- *"As a user I `kubectl apply` YAML objects; controllers do the rest and **keep** it true."*

➡️ **Next:** [01 — Architecture big picture](./01_Architecture_Big_Picture.md)
