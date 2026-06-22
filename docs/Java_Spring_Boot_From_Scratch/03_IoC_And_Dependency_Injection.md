# 03 — IoC & Dependency Injection

## 🧠 The One Idea

**Instead of your code creating the objects it needs, Spring creates them and hands them in — you
declare *what* you need, the framework supplies it.** That inversion ("don't call us, we'll call
you") is the heart of Spring: you write business logic, the container wires the plumbing.

The one-liner: **"IoC means the container owns object creation and wiring; dependency injection is how
it hands collaborators to your class, so code is decoupled and testable."**

---

## 1. Inversion of Control (IoC)

- **Traditional:** your class does `new EmailService()` — it controls creation, and is now coupled to
  the concrete type.
- **IoC:** the **container** creates objects and injects them. Control of creation is *inverted* to
  the framework.

---

## 2. Dependency Injection (the mechanism)

- **Constructor injection (preferred):** dependencies are constructor args → final, required,
  testable.

```java
@Service
class OrderService {
    private final PaymentClient payments;
    OrderService(PaymentClient payments) {   // Spring injects this
        this.payments = payments;
    }
}
```

- Field/setter injection exist but constructor injection is the recommended default.

---

## 3. Beans & the container

- A **bean** is any object Spring manages.
- The **ApplicationContext** is the container holding all beans and their wiring.
- **Stereotype annotations** register beans: `@Component`, `@Service`, `@Repository`,
  `@Controller`; `@Configuration` + `@Bean` for explicit creation.
- `@Autowired` requests injection (implicit on a single constructor).

---

## 4. Why it matters (the payoff)

- **Decoupling:** depend on an **interface**, not a concrete class → swap implementations freely.
- **Testability:** inject a **mock** in tests instead of the real dependency — huge for unit tests.
- **Single responsibility:** classes focus on logic, not on constructing their collaborators.
- **Configuration in one place:** the container, not scattered `new` calls.

---

## 5. Bean scopes & lifecycle

- **Scopes:** `singleton` (default — one shared instance), `prototype` (new each time), plus web
  scopes (`request`, `session`).
- **Lifecycle hooks:** `@PostConstruct` / `@PreDestroy` for init/cleanup.
- Singletons are shared, so keep them **stateless** (or thread-safe) — a common bug source.

---

## 🎤 Say this in the interview

- *"IoC inverts object creation to the container; dependency injection is how it supplies
  collaborators — I prefer constructor injection for final, required, testable dependencies."*
- *"The win is decoupling and testability: I depend on interfaces and inject mocks in unit tests
  instead of `new`-ing concrete classes."*
- *"Beans are container-managed; default scope is singleton, so I keep singletons stateless or
  thread-safe."*

➡️ **Next:** [04 — Building a REST API](./04_Building_A_REST_API.md)
