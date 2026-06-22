# 05 — Data & Transactions

## 🧠 The One Idea

**Spring Data JPA lets you talk to the database in Java objects instead of SQL, and `@Transactional`
draws the "all-or-nothing" boundary around your work.** You define an entity and a repository
interface; Spring generates the queries. You annotate a service method; Spring manages the
transaction.

The one-liner: **"JPA maps objects to tables (ORM); `@Transactional` wraps a unit of work so it
commits together or rolls back together."**

---

## 1. Entities & repositories

```java
@Entity
class User {
    @Id @GeneratedValue Long id;
    String name;
}

interface UserRepository extends JpaRepository<User, Long> {
    List<User> findByName(String name);   // Spring generates the query from the name
}
```

- **`@Entity`** maps a class to a table; fields to columns.
- **`JpaRepository`** gives CRUD for free; **derived query methods** (`findByName`) are generated
  from the method name. Custom queries via **`@Query`** (JPQL or native SQL).

---

## 2. @Transactional

```java
@Service
class TransferService {
    @Transactional
    void transfer(Long from, Long to, BigDecimal amt) {
        debit(from, amt);
        credit(to, amt);      // both commit, or both roll back
    }
}
```

- Wraps the method in a DB transaction — commit on success, **rollback on a runtime exception**.
- Put it on the **service** layer (the business unit of work), not the controller.
- Gotcha: by default it rolls back on **unchecked** exceptions, not checked ones (configurable).

---

## 3. Propagation & isolation

- **Propagation:** how nested transactional calls behave — `REQUIRED` (default, join the existing
  one), `REQUIRES_NEW` (suspend and start a fresh one).
- **Isolation:** maps to DB isolation levels (Databases course Lesson 02) — `READ_COMMITTED`, etc.
- A self-invocation gotcha: calling another `@Transactional` method on `this` **bypasses the proxy**
  → the annotation is ignored.

---

## 4. The N+1 problem (must-know)

- Lazily loading a collection per parent row → **1 query + N queries** = a performance killer.
- Fixes: **`JOIN FETCH`**, an **`@EntityGraph`**, or batch fetching.
- Choose **`LAZY`** vs **`EAGER`** loading deliberately; lazy is the safer default but watch for
  `LazyInitializationException` outside a session.

---

## 5. Connection pooling & performance

- Spring Boot uses **HikariCP** (a fast connection pool) by default — reuse connections instead of
  opening one per request.
- Tune **pool size** to the DB's capacity; too many connections overwhelm the DB.
- Use **pagination** (`Pageable`) for large result sets; never load an unbounded table.

---

## 🎤 Say this in the interview

- *"JPA is an ORM mapping entities to tables; JpaRepository gives CRUD and derived queries, with
  `@Query` for custom ones."*
- *"`@Transactional` on the service layer makes a unit of work atomic — commit together or roll back
  on a runtime exception — and I'm careful with propagation and self-invocation bypassing the
  proxy."*
- *"I watch for the N+1 problem and fix it with JOIN FETCH or entity graphs, and rely on HikariCP
  pooling with sensible sizing and pagination."*

➡️ **Next:** [06 — Hands-on lab](./06_Hands_On_Lab.md)
