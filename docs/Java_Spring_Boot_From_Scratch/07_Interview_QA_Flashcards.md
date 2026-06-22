# 07 — Interview Q&A Flashcards

Rapid-fire. Cover the answer, say it aloud, then check. Read twice.

---

## JVM & language

**Q: Why is Java platform-independent?**
Source compiles to bytecode that runs on the JVM, which translates it to the host at runtime.

**Q: JDK vs JRE vs JVM?**
JDK = develop (compiler + tools); JRE = JVM + libraries to run; JVM = the execution engine.

**Q: What does the JIT do?**
Compiles hot bytecode methods to native code at runtime for speed.

**Q: How is memory managed?**
A generational garbage collector (young/old gens); G1 is the modern default.

---

## OOP & collections

**Q: Interface vs abstract class?**
Interface = capability contract, multiple inheritance, no state; abstract class = shared base with
state/constructors, single inheritance.

**Q: equals/hashCode rule?**
Override both together; equal objects must have equal hashCodes or hash collections break.

**Q: ArrayList vs LinkedList?**
ArrayList = fast random access; LinkedList = fast insert/remove at ends.

**Q: == vs .equals()?**
`==` compares references; `.equals()` compares value.

---

## Concurrency

**Q: volatile vs synchronized?**
volatile = visibility only (no atomicity); synchronized = visibility + atomicity for critical
sections.

**Q: Make a counter thread-safe?**
AtomicInteger (CAS) or a lock — not volatile alone.

**Q: How to avoid deadlock?**
Consistent lock ordering; prefer java.util.concurrent collections.

**Q: Better than raw threads?**
ExecutorService thread pools with Future/CompletableFuture.

---

## Spring core

**Q: What is IoC?**
The container owns object creation/wiring; control is inverted from your code to the framework.

**Q: What is DI, preferred form?**
Supplying dependencies to a class; constructor injection (final, required, testable).

**Q: What's a bean and default scope?**
A container-managed object; default scope singleton (keep it stateless).

---

## Spring web & data

**Q: The three layers?**
Controller (HTTP) → service (logic + transaction) → repository (persistence).

**Q: What does @Transactional do?**
Wraps a method in a DB transaction — commit together, roll back on a runtime exception.

**Q: Where do you put @Transactional?**
On the service layer; beware self-invocation bypassing the proxy.

**Q: The N+1 problem?**
1 query + N lazy child queries; fix with JOIN FETCH or @EntityGraph.

**Q: What does Spring Boot add?**
Auto-configuration, starters, embedded server (runnable JAR), Actuator.

---

🎉 **You finished Java & Spring Boot.** Three-sentence core: **bytecode on the JVM with GC**, **IoC
container injects dependencies**, **controller→service→repository with `@Transactional` as the
consistency boundary.** For mixed Java/Go stacks, this plus the Go course covers both sides.
