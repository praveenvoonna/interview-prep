# 06 — Hands-On Lab

## 🧠 The One Idea

**Build one tiny CRUD service end-to-end — entity, repository, service, controller — and the whole
Spring stack clicks.** It's the same three-layer shape you'll see in every real Spring app.

The one-liner: **"A 4-file CRUD service shows the controller→service→repository flow and Spring
Boot's zero-config wiring."**

---

## 1. Generate the project

Use **[start.spring.io](https://start.spring.io)** with dependencies: **Web**, **Spring Data JPA**,
**H2** (in-memory DB), **Validation**. Or Maven:

```xml
<dependency><groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-web</artifactId></dependency>
<dependency><groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-jpa</artifactId></dependency>
<dependency><groupId>com.h2database</groupId>
  <artifactId>h2</artifactId><scope>runtime</scope></dependency>
```

---

## 2. Entity + repository

```java
@Entity
class Task {
    @Id @GeneratedValue Long id;
    @NotBlank String title;
    boolean done;
    // getters/setters
}

interface TaskRepository extends JpaRepository<Task, Long> {}
```

---

## 3. Service

```java
@Service
class TaskService {
    private final TaskRepository repo;
    TaskService(TaskRepository repo) { this.repo = repo; }

    List<Task> all() { return repo.findAll(); }
    Task create(Task t) { return repo.save(t); }
    Task get(Long id) { return repo.findById(id)
        .orElseThrow(() -> new ResponseStatusException(HttpStatus.NOT_FOUND)); }
}
```

---

## 4. Controller

```java
@RestController
@RequestMapping("/tasks")
class TaskController {
    private final TaskService service;
    TaskController(TaskService service) { this.service = service; }

    @GetMapping            List<Task> all()                 { return service.all(); }
    @GetMapping("/{id}")   Task one(@PathVariable Long id)  { return service.get(id); }
    @PostMapping @ResponseStatus(HttpStatus.CREATED)
    Task add(@RequestBody @Valid Task t)                    { return service.create(t); }
}
```

---

## 5. Run & test

```bash
./mvnw spring-boot:run

curl -X POST localhost:8080/tasks -H 'Content-Type: application/json' \
     -d '{"title":"prep interview"}'
curl localhost:8080/tasks
curl localhost:8080/tasks/1
# H2 console at /h2-console; Actuator health at /actuator/health
```

**Extensions:** add `PUT`/`DELETE`; add validation error handling with `@ControllerAdvice`; swap H2
for Postgres; add a `@Transactional` multi-step method; write a `@WebMvcTest` + `@DataJpaTest`.

---

## 🎤 Say this in the interview

- *"I can stand up a CRUD service in four files — entity, repository, service, controller — and Spring
  Boot auto-wires it with an embedded server and DB."*
- *"The repository gives CRUD for free via JpaRepository; the service holds logic and the transaction
  boundary; the controller just handles HTTP."*
- *"I'd test it with `@WebMvcTest` for the web layer and `@DataJpaTest` for persistence, and expose
  health via Actuator."*

➡️ **Next:** [07 — Interview Q&A flashcards](./07_Interview_QA_Flashcards.md)
