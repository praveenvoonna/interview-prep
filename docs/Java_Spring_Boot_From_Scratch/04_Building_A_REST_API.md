# 04 — Building a REST API

## 🧠 The One Idea

**A Spring Boot web app is three layers in a line: a controller takes the HTTP request, a service
does the work, a repository talks to the database.** Each layer has one job, and Spring wires them
together. Learn this flow and you can read or build almost any Spring service.

The one-liner: **"Controller → service → repository: the controller handles HTTP, the service holds
business logic, the repository handles persistence."**

---

## 1. The three layers

```
HTTP request → @RestController → @Service → @Repository → DB
                  (web/IO)       (logic)     (data access)
```

- **Controller:** maps URLs to methods, parses input, returns responses. No business logic.
- **Service:** the business rules; transaction boundary (Lesson 05).
- **Repository:** CRUD against the DB (Spring Data).

---

## 2. A controller

```java
@RestController
@RequestMapping("/users")
class UserController {
    private final UserService service;
    UserController(UserService service) { this.service = service; }

    @GetMapping("/{id}")
    User get(@PathVariable Long id) { return service.find(id); }

    @PostMapping
    @ResponseStatus(HttpStatus.CREATED)
    User create(@RequestBody @Valid CreateUser req) { return service.create(req); }
}
```

- `@GetMapping/@PostMapping/...` map HTTP verbs; `@PathVariable`, `@RequestParam`, `@RequestBody`
  bind input; `@Valid` triggers validation.

---

## 3. JSON & the request lifecycle

- Spring auto-converts objects ↔ JSON via **Jackson** (`@RequestBody` in, return value out).
- Flow: **DispatcherServlet** → finds the handler → binds/validates args → calls your method →
  serializes the return → HTTP response.
- Use **DTOs** for the API surface, separate from your DB entities (don't leak the schema).

---

## 4. Error handling

- Throw meaningful exceptions; centralize translation with **`@ControllerAdvice` +
  `@ExceptionHandler`** → consistent JSON error bodies and proper status codes.
- Map: bad input → **400**, not found → **404**, conflict → **409**, server fault → **500**.
- Validate at the edge (`@Valid` + bean-validation annotations like `@NotNull`, `@Size`).

---

## 5. What Spring Boot adds

- **Auto-configuration:** sensible defaults from classpath (embedded **Tomcat**, JSON, etc.) — no XML.
- **Starters:** curated dependency bundles (`spring-boot-starter-web`, `-data-jpa`).
- **Embedded server:** the app is a runnable JAR (`java -jar`) — ideal for containers.
- **Actuator:** health/metrics endpoints out of the box (ties to the Observability course).

---

## 🎤 Say this in the interview

- *"A Spring web app is three layers: controller for HTTP, service for business logic and the
  transaction boundary, repository for persistence."*
- *"Controllers bind and validate input, Jackson handles JSON, and I expose DTOs rather than leaking
  entities; I centralize errors with `@ControllerAdvice`."*
- *"Spring Boot's auto-configuration, starters, and embedded Tomcat give a runnable JAR with health
  and metrics via Actuator — perfect for containers."*

➡️ **Next:** [05 — Data & transactions](./05_Data_And_Transactions.md)
