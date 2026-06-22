# 01 — OOP & Collections

## 🧠 The One Idea

**Java is built on objects and a rich set of ready-made data structures.** OOP organizes code around
types that bundle data + behaviour; the Collections Framework gives you battle-tested List/Map/Set
implementations so you rarely build your own. Interviews probe two things hard:
**interface-vs-abstract** and **equals/hashCode**.

The one-liner: **"Code to interfaces, override equals & hashCode together, and pick the collection by
its access pattern."**

---

## 1. The four OOP pillars

- **Encapsulation:** hide fields (private), expose behaviour via methods.
- **Inheritance:** `extends` to reuse/specialize.
- **Polymorphism:** one interface, many implementations (overriding).
- **Abstraction:** expose *what*, hide *how*.

---

## 2. Interface vs abstract class (classic Q)

| | Interface | Abstract class |
|---|---|---|
| Inherit | **many** (multiple) | **one** (single) |
| State | constants (+ default methods) | can have fields/constructors |
| Use when | a **capability/contract** | a **shared base** with common code |

Since Java 8 interfaces can have **default methods**, but they still hold no instance state. Rule:
**"is-a shared base" → abstract; "can-do capability" → interface.**

---

## 3. The collection families

- **List** (ordered, duplicates): `ArrayList` (fast random access), `LinkedList` (fast
  insert/remove at ends).
- **Set** (no duplicates): `HashSet` (O(1), unordered), `TreeSet` (sorted), `LinkedHashSet` (insertion
  order).
- **Map** (key→value): `HashMap` (O(1)), `TreeMap` (sorted), `LinkedHashMap` (order).

Pick by access pattern: random index → ArrayList; uniqueness → Set; lookup by key → Map.

---

## 4. equals() & hashCode() (the trap)

- **Contract:** if `a.equals(b)` then `a.hashCode() == b.hashCode()`. **Always override both
  together.**
- Hash-based collections (`HashMap`/`HashSet`) use `hashCode` to find the bucket, then `equals` to
  confirm — break the contract and lookups silently fail.
- Use immutable fields for keys; `Objects.equals`/`Objects.hash` to implement.

---

## 5. Generics & a few essentials

- **Generics** give compile-time type safety: `List<String>` not raw `List` → no casts, no
  `ClassCastException`.
- **Type erasure:** generics exist at compile time, erased at runtime (JVM sees raw types).
- **Autoboxing:** `int` ↔ `Integer` automatically (watch the performance cost in tight loops).
- **`==` vs `.equals()`:** `==` compares references; `.equals()` compares value — a perennial bug.

---

## 🎤 Say this in the interview

- *"I code to interfaces — a capability contract with multiple inheritance — and use abstract classes
  for a shared base with common state/code."*
- *"I always override equals and hashCode together; hash collections find the bucket via hashCode then
  confirm with equals, so breaking the contract breaks lookups."*
- *"I pick collections by access pattern — ArrayList for indexing, HashSet for uniqueness, HashMap for
  key lookup — and use generics for compile-time type safety."*

➡️ **Next:** [02 — Concurrency](./02_Concurrency.md)
