# 00 — JVM, JDK, JRE

## 🧠 The One Idea

**Java compiles to a portable middle-language (bytecode) that runs on a virtual machine, so the same
program runs anywhere there's a JVM.** "Write once, run anywhere": you don't compile to a specific
CPU — you compile to bytecode, and the JVM translates it to the host machine at runtime.

The one-liner: **"Java source → bytecode → the JVM runs it on any platform, JIT-compiling hot paths
to native code; the GC manages memory."**

---

## 1. JDK vs JRE vs JVM

| Term | Is | Contains |
|---|---|---|
| **JVM** | the runtime engine | executes bytecode, GC, JIT |
| **JRE** | run Java apps | JVM + standard libraries |
| **JDK** | **develop** Java apps | JRE + compiler (`javac`), tools |

**Develop with the JDK; the JRE just runs.** (Modern Java often ships the JDK only.)

---

## 2. How Java runs

```
Foo.java  ──javac──►  Foo.class (bytecode)  ──JVM──►  runs on any OS/CPU
```

- **`javac`** compiles source to **bytecode** (platform-independent).
- The **JVM** loads it (class loader), verifies it, and executes.
- The **JIT compiler** spots "hot" methods and compiles them to **native code** at runtime → starts
  interpreted, gets fast where it matters.

---

## 3. Memory: heap, stack, GC

- **Heap:** where objects live (shared) — managed by the GC.
- **Stack:** per-thread; holds frames, local variables, references.
- **Garbage Collector:** automatically frees objects no longer reachable → no manual `free()`.

This is the big contrast with Go (also GC'd) and C/C++ (manual).

---

## 4. Garbage collection basics

- Most objects die young → the heap is split into **young** (Eden + survivor) and **old**
  generations (**generational GC**).
- **Minor GC** cleans the young gen often (cheap); **major/full GC** cleans the old gen (costlier).
- Collectors to name: **G1** (default, balanced), **ZGC/Shenandoah** (low-pause, large heaps).
- The tuning trade-off is **throughput vs pause time**.

---

## 5. Things interviewers probe

- **`-Xms`/`-Xmx`:** initial/max heap size.
- **Stop-the-world pauses:** GC moments where app threads pause — minimized by modern collectors.
- **Memory leaks in Java?** Yes — via lingering references (e.g. static collections that grow) the
  GC can't collect.
- **Platform independence** is the headline benefit; **bytecode + JVM** is the mechanism.

---

## 🎤 Say this in the interview

- *"Java compiles to bytecode that runs on the JVM, so it's platform-independent; the JIT compiles
  hot methods to native code at runtime for speed."*
- *"JDK is for developing (compiler + tools), JRE is JVM plus libraries to run — the JVM is the
  engine."*
- *"Memory is automatic via a generational GC — young gen for short-lived objects, old gen for
  survivors — and G1 is the modern default balancing throughput and pause time."*

➡️ **Next:** [01 — OOP & collections](./01_OOP_And_Collections.md)
