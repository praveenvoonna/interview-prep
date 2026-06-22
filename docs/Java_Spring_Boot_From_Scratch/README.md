# ☕ Java & Spring Boot From Scratch — A Refresher Course (in plain English)

A focused refresher on **Java + Spring Boot** — still a default enterprise backend stack, common
in mixed Java/Go shops. This blows the dust off the concepts that come up in Java-stack interviews.

> **How to read this:** go in order, 00 → 07. Each lesson starts with a plain-English analogy
> (**"The One Idea"**), then the real mechanics, then **"Say this in the interview"** lines.

> **Status:** 📝 *Syllabus ready — lessons to be written.* This README is the outline and the
> reference you can study from today.

---

## 📂 The lessons (planned, read in order)

### Part A — Core Java that interviews probe
- **00 — JVM, JDK, JRE** — bytecode, the JVM, GC basics, how Java runs.
- **01 — OOP & collections** — interfaces vs abstract, equals/hashCode, List/Map/Set, generics.
- **02 — Concurrency** — threads, `ExecutorService`, `synchronized`, `volatile`, the memory model.

### Part B — Spring Boot
- **03 — IoC & dependency injection** — beans, the container, why DI matters.
- **04 — Building a REST API** — controllers, services, repositories, request/response lifecycle.
- **05 — Data & transactions** — Spring Data JPA, `@Transactional`, connection pooling.

### Part C — Practice
- **06 — Hands-on lab** — a small Spring Boot REST service with a DB.
- **07 — Interview Q&A flashcards** — JVM/collections/Spring rapid-fire.

---

## 🧠 The whole course in three sentences
1. **Java runs on the JVM:** write once, compile to bytecode, run anywhere — and the GC manages
   memory for you.
2. **Spring Boot is convention over configuration:** the IoC container wires your beans so you
   write business logic, not plumbing.
3. **Most Spring web apps are three layers:** controller → service → repository, with
   `@Transactional` drawing the consistency boundary.

---

## 🔗 Companion material
- **[Databases From Scratch](../Databases_From_Scratch/)** — JPA sits on top of these.
- **[gRPC From Scratch](../gRPC_From_Scratch/)** — the alternative to REST for service calls.

When the lessons are written, start at **00**.
