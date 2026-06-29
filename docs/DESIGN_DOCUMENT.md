# TutorFlow Backend Platform — Complete Design Document

**Project:** TutorFlow (Educational Tutoring Management Backend)  
**Author:** Leo / Yingbo Tong  
**Date:** 2026-06-29  
**Duration:** 4-5 weeks  
**Purpose:** Build a real backend system for Leo's tutoring business while learning deep engineering concepts, NOT vibe-coding.

---

## QUICK REFERENCE FOR AI SESSIONS

**You are:** AI helping Leo learn backend engineering through building TutorFlow  
**Leo is:** 24-year-old backend engineer at TD, wanting to build portfolio + tutoring platform  
**Your job:** Explain concepts, review code, guide learning — **NOT** write working code for him  
**Success metric:** Leo can explain every decision, modify design, and debug independently  

**If Leo asks for code:** Provide scaffolds with `// TODO Leo:` comments. Make him complete it.  
**If Leo is stuck:** Ask clarifying questions. Make him find the answer.  
**If Leo succeeded at something:** Ask him to teach you how it works (forces verbalization).

---

## CRITICAL NOTE FOR AI SESSIONS

**If Leo asks you to help with TutorFlow, read this section FIRST before proceeding.**

This is a **learning-first project**. The goal is not a finished product; it's for Leo to become expert-level in backend architecture. Your role is **teacher/reviewer, not code-writer**.

### What Leo MUST Do Himself (Non-Negotiable)

**DO NOT DO THESE FOR LEO.** If he asks, explain the concept and ask him to implement:

- **Phase 1 (Database):** Leo writes every SQL migration file. He decides table structure, constraints, indices. AI explains normalization theory; Leo applies it.
- **Phase 2 (Domain):** Leo writes all domain entities, value objects, and service classes. AI shows DDD patterns; Leo implements business logic.
- **Phase 3 (Testing):** Leo writes all test cases. AI explains testing pyramid; Leo thinks of test scenarios and implements assertions.
- **Phase 4 (API):** Leo designs REST endpoints on paper first. Then Leo codes all Spring @RestController classes. AI explains REST principles; Leo codes.
- **Phase 5 (Async):** Leo codes async notifications and retry logic. AI explains @Async/@Retryable; Leo integrates into codebase.
- **Phase 6 (Ops):** Leo writes Dockerfile and docker-compose.yml. AI explains Docker best practices; Leo builds config files.

**Exception:** If Leo gets syntax errors or doesn't understand what an error message means, you can help debug. But you should NOT rewrite his code to fix it; instead, point him to the issue and ask him to fix it.

### What AI SHOULD Do

✅ **Safe AI Actions:**
- Explain concepts with examples (normalization, DDD, REST principles, Docker)
- Suggest reading materials (links to articles, docs)
- Scaffold empty file structure and directory layout
- Review Leo's code and ask clarifying questions
- Explain error messages and stack traces
- Point out potential design issues
- Provide incomplete code snippets with `// TODO Leo: implement X` comments
- Recommend libraries, versions, and configurations
- Explain tradeoffs between design approaches
- Answer "why is this better than that?" questions

❌ **DO NOT DO:**
- Write working, copy-paste-ready code for Leo
- Complete Leo's incomplete implementations without him asking
- Run `mvn clean build` or commit/push for him (he owns these actions)
- Suggest he skip sections ("that's too hard, let me handle it")
- Generate test cases; ask him to think of scenarios

### How to Know If Leo Actually Learned

After each phase, Leo should be able to:
1. **Explain every decision:** "Why does Student have this relationship to Session?"
2. **Modify the design:** "What if sessions can be rescheduled? How does schema change?"
3. **Teach it:** "Explain this to a junior engineer who's never done DDD"
4. **Debug independently:** "This test is failing. What's wrong and how do I fix it?"
5. **Write new features:** "Add a Tutor entity and update services. I'll do it myself"

**Red flags (means he didn't learn):**
- Asks "why did I do this?" (should know)
- Can't modify the code without asking for help
- Wants to copy-paste a new feature instead of building it
- Doesn't understand his own test assertions

If you see these flags, **push back**. Ask him to re-read the phase documentation and think through it himself.

---

## Project Overview

### Business Context
Leo runs a tutoring business targeting Ontario Grade 7–12 students. He needs a backend platform to:
- Manage student profiles and sessions
- Handle booking conflicts (no overbooking)
- Process payments
- Track progress
- Generate invoices
- Send reminders

### Technical Context
- **Stack:** Java 21, Spring Boot 3.x, PostgreSQL, Maven
- **Deployment:** Docker + docker-compose
- **Quality:** 80%+ test coverage, clean code, documentation
- **Learning Goals:** Database design, domain-driven design, testing, REST, async, DevOps

### Non-Goals
- Frontend (not required)
- Microservices (single monolith is fine)
- Advanced caching or load balancing
- Machine learning or recommendation engines
- Mobile app

---

## Phase 1: Database Design (1 week)

### Learning Objectives
By end of Phase 1, Leo will understand:
- Relational database design principles (normalization, constraints)
- How to think about data relationships
- Migration tools (Flyway)
- Schema versioning and evolution
- Index design and query performance

### What Leo Will Learn (Concepts)
1. **Normalization (1NF, 2NF, 3NF):** Eliminate data redundancy, ensure data integrity
2. **Constraints:** PRIMARY KEY, FOREIGN KEY, UNIQUE, CHECK, NOT NULL
3. **Relationships:** One-to-many, many-to-many, modeling choices
4. **Indices:** When to index, performance impact, tradeoffs
5. **Migrations:** Version control for schema, Flyway best practices

### Deliverables

#### 1.1 — Entity Relationship Diagram (Pen and Paper)
**Leo does this:** Draw entities and relationships:
- Student (id, name, grade, email, phone, created_at)
- Tutor (id, name, specialties, rates_by_grade)
- Session (id, student_id, tutor_id, start_time, duration_minutes, status, notes)
- Payment (id, session_id, amount, status, payment_date)
- Invoice (id, student_id, period, total_amount, payment_received)

**Leo thinks about:**
- "Why is Tutor separate from Student?"
- "Can a Session exist without a Payment? When?"
- "Should Session store tutor ID or tutor name? Why?"
- "What happens if a student is deleted?"

**AI can help with:** Explaining one-to-many vs many-to-many relationships, drawing tips.

#### 1.2 — SQL Schema Definition

**Leo writes:** Flyway migration V001__Initial_schema.sql

This migration should include:
- `students` table with constraints
- `tutors` table with constraints
- `sessions` table with foreign keys and constraints
- `payments` table with status enum or check constraint
- `invoices` table

**Example structure (Leo must complete):**
```sql
CREATE TABLE students (
  id BIGINT PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
  name VARCHAR(255) NOT NULL,
  email VARCHAR(255) UNIQUE NOT NULL,
  phone VARCHAR(20),
  grade INT NOT NULL CHECK (grade >= 7 AND grade <= 12),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Leo adds: tutors table
-- Leo adds: sessions table (with FK constraints)
-- Leo adds: payments table (with check for amount > 0)
-- Leo adds: invoices table
```

**Leo must decide:**
- Should session cancellations soft-delete or hard-delete?
- What columns should be nullable vs NOT NULL?
- What constraints enforce business rules?
- Which columns need indices for query performance?

**AI role:** Explain why constraints matter; Leo writes them.

#### 1.3 — Documentation: `docs/DATABASE_DESIGN.md`

**Leo writes:** For each table, explain:
- What it represents in the business
- Why each column exists
- What constraints apply and why
- What indices exist and why
- Any denormalization decisions and tradeoffs

**Example:**
```markdown
## sessions table

Represents a tutoring session between a student and tutor.

### Columns
- `id`: Unique session identifier (PK)
- `student_id`: FK to students (NOT NULL, sessions require a student)
- `tutor_id`: FK to tutors (NOT NULL, sessions require a tutor)
- `start_time`: DateTime when session begins
- `duration_minutes`: Length of session (CHECK duration_minutes > 0)
- `status`: ENUM ('BOOKED', 'COMPLETED', 'CANCELLED')
- `notes`: Optional notes from tutor about student progress

### Constraints
- (student_id, start_time, duration_minutes): Should be unique? Why or why not?
- status: Only allow valid values

### Indices
- INDEX on (student_id, start_time): Fast conflict detection
- INDEX on tutor_id: Fast "list sessions for tutor"

### Why These Decisions
[Leo explains the reasoning]
```

**AI role:** Review Leo's reasoning; ask follow-up questions.

---

## Phase 2: Domain Model & Service Layer (1 week)

### Learning Objectives
By end of Phase 2, Leo will understand:
- Domain-Driven Design (DDD) fundamentals
- Entity vs DTO vs domain model
- Service layer pattern and responsibility
- Dependency injection and loose coupling
- Business rule enforcement in code (not DB only)

### What Leo Will Learn (Concepts)
1. **Domain Entities:** Objects that represent business concepts (Student, Session, Payment)
2. **Value Objects:** Objects without identity (Money, TimeSlot)
3. **Aggregates:** Groups of entities + value objects (Student aggregate includes Sessions)
4. **Services:** Stateless objects that orchestrate domain logic
5. **Repositories:** Abstraction for persistence (not direct DB queries in business logic)

### Deliverables

#### 2.1 — Domain Entity Classes

**Leo writes:** Plain Java objects representing domain concepts.

```java
// src/main/java/com/tutorflow/domain/Student.java

public class Student {
  private String id;
  private String name;
  private int grade;
  private LocalDateTime createdAt;
  
  // Business rules as methods, not getters
  public boolean canBookSessionAt(LocalDateTime startTime, int durationMinutes) {
    // Leo implements conflict detection logic here
    // If session time overlaps existing session, return false
  }
  
  public void addSession(Session session) {
    // Leo enforces: cannot add overlapping sessions
  }
}
```

**Leo must decide:**
- What state belongs in Student?
- What methods enforce business rules?
- Should Student know about all Sessions or just ask a repository?
- What exceptions should domain entities throw?

**What Leo should NOT do:**
- Add @Entity, @Table annotations yet (that's infrastructure)
- Use database column names in field names
- Make it a JPA entity

**AI role:** Explain DDD concepts; Leo writes domain objects.

#### 2.2 — Service Layer

**Leo writes:** Service classes that orchestrate domain logic.

```java
// src/main/java/com/tutorflow/service/SessionService.java

public class SessionService {
  private StudentRepository studentRepository;
  private SessionRepository sessionRepository;
  
  // Constructor injection (Leo must understand why, not just copy-paste)
  public SessionService(StudentRepository studentRepository, SessionRepository sessionRepository) {
    this.studentRepository = studentRepository;
    this.sessionRepository = sessionRepository;
  }
  
  public Session bookSession(String studentId, LocalDateTime startTime, int durationMinutes, String tutorId) {
    // Leo implements:
    // 1. Load student from repository
    // 2. Check if student can book at this time
    // 3. If no, throw exception with reason
    // 4. Create session
    // 5. Save to repository
    // 6. Return session
  }
  
  public void cancelSession(String sessionId, String reason) {
    // Leo implements cancellation logic
    // What state transitions are valid? (BOOKED -> CANCELLED, but not COMPLETED -> CANCELLED?)
  }
}
```

**Leo must decide:**
- What should StudentService do vs SessionService?
- What exceptions should services throw?
- Should service talk to multiple repositories or just one?
- What validates input (domain entity or service)?

**AI role:** Explain service patterns; Leo writes service logic.

#### 2.3 — Repository Interface

**Leo writes:** Abstraction for data access (no implementation yet).

```java
// src/main/java/com/tutorflow/repository/StudentRepository.java

public interface StudentRepository {
  Student findById(String id);
  void save(Student student);
  void delete(String id);
  List<Student> findByGrade(int grade);
}
```

**Leo must decide:**
- What queries does the app need?
- What methods belong on this repository?
- Should repository return domain entities or DTOs?

**AI role:** Explain repository pattern; Leo designs interface.

#### 2.4 — Documentation: `docs/DOMAIN_DESIGN.md`

**Leo writes:**
- What is an entity, value object, aggregate?
- What is the Student aggregate? What does it contain?
- What are the key business rules? (e.g., "no overlapping sessions")
- How are services organized? What is each service responsible for?
- Why are repositories abstractions?

**Example:**
```markdown
## Student Aggregate

Student is the root entity of its aggregate. An aggregate includes:
- Student entity (id, name, grade, email)
- Sessions (as part of Student? or separate aggregate?)

### Key Invariants (Business Rules)
- A student cannot have two sessions at the same time
- A student grade must be 7–12
- A student can only have one active invoice per billing period

### Why This Design
[Leo explains reasoning]

## Session Service Responsibilities
- Create sessions (validate business rules)
- Cancel sessions
- Find sessions by student
- Calculate conflicts
```

**AI role:** Review Leo's understanding; ask clarifying questions.

---

## Phase 3: Testing Strategy & Implementation (1 week)

### Learning Objectives
By end of Phase 3, Leo will understand:
- Test pyramid (unit, integration, e2e)
- Meaningful assertions vs "just running code"
- Testcontainers for isolation
- Test fixtures and factories
- Coverage as a tool, not a goal

### What Leo Will Learn (Concepts)
1. **Unit Tests:** Test domain logic in isolation (no database, no Spring)
2. **Integration Tests:** Test services + database together (using Testcontainers)
3. **Test Fixtures:** Reusable test data builders
4. **Assertions:** What makes a good assertion
5. **Coverage:** Why 80% is target, not 100%

### Deliverables

#### 3.1 — Unit Tests for Domain Entities

**Leo writes:** Tests in `src/test/java/com/tutorflow/domain/StudentTest.java`

```java
public class StudentTest {
  
  @Test
  void studentCanBookSessionAtAvailableTime() {
    // Arrange
    Student student = new Student("123", "Leo", 11);
    
    // Act
    boolean canBook = student.canBookSessionAt(
      LocalDateTime.of(2026, 6, 29, 10, 0),
      60
    );
    
    // Assert
    assertTrue(canBook, "Student should be able to book at available time");
  }
  
  @Test
  void studentCannotBookOverlappingSession() {
    // Arrange
    Student student = new Student("123", "Leo", 11);
    student.addSession(new Session(..., LocalDateTime.of(2026, 6, 29, 10, 0), 60));
    
    // Act & Assert
    assertThrows(SessionOverlapException.class, () -> {
      student.canBookSessionAt(LocalDateTime.of(2026, 6, 29, 10, 30), 60);
    });
  }
}
```

**Leo must write:**
- Test cases for all business rules
- Meaningful test names (not `testStudent()`)
- Arrange-Act-Assert structure
- Assertions that verify behavior, not just "it didn't crash"

**What NOT to test:**
- Getters/setters
- Empty try-catch blocks
- "Happy path only"

**AI role:** Explain testing patterns; Leo writes test cases.

#### 3.2 — Integration Tests with Testcontainers

**Leo writes:** Tests in `src/test/java/com/tutorflow/integration/SessionServiceIntegrationTest.java`

```java
@Testcontainers
@SpringBootTest
public class SessionServiceIntegrationTest {
  
  @Container
  static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:15");
  
  @Autowired
  SessionService sessionService;
  
  @Autowired
  StudentRepository studentRepository;
  
  @Test
  void bookSessionPersistsToDatabase() {
    // Arrange
    Student student = new Student(...);
    studentRepository.save(student);
    
    // Act
    Session session = sessionService.bookSession(
      student.getId(),
      LocalDateTime.of(2026, 6, 29, 10, 0),
      60,
      "tutor123"
    );
    
    // Assert
    Session retrieved = sessionRepository.findById(session.getId());
    assertEquals(session.getId(), retrieved.getId());
    assertEquals("BOOKED", retrieved.getStatus());
  }
}
```

**Leo must:**
- Write integration tests for key workflows
- Understand Testcontainers setup and teardown
- Test database persistence and retrieval
- Think about test data setup

**AI role:** Explain Testcontainers; Leo writes integration tests.

#### 3.3 — Test Fixtures & Builders

**Leo writes:** Reusable test data in `src/test/java/com/tutorflow/testdata/StudentBuilder.java`

```java
public class StudentBuilder {
  private String id = UUID.randomUUID().toString();
  private String name = "Leo";
  private int grade = 11;
  
  public StudentBuilder withGrade(int grade) {
    this.grade = grade;
    return this;
  }
  
  public Student build() {
    return new Student(id, name, grade);
  }
}

// Usage in test:
Student student = new StudentBuilder().withGrade(9).build();
```

**Leo must:**
- Create builders for complex domain objects
- Use builders to make tests readable
- Avoid copy-pasting test data

**AI role:** Show builder pattern; Leo implements builders.

#### 3.4 — Documentation: `docs/TESTING_STRATEGY.md`

**Leo writes:**
- Test pyramid: unit vs integration vs e2e split
- What gets tested at each level
- Why Testcontainers
- How coverage is measured
- When coverage stops being useful

**Example:**
```markdown
## Test Pyramid

```
       /\
      /  \  E2E (1-2 tests)
     /____\
    /      \
   /  Integ \  Integration (10-15 tests)
  /________\
 /          \
/ Unit Tests \  Unit Tests (50-70 tests)
/____________\
```

### Unit Tests
- Test domain logic in isolation
- No database, no Spring
- Target: 100% coverage of business rules

### Integration Tests
- Test services + database
- Use Testcontainers for PostgreSQL
- Target: Key workflows (book, cancel, pay)

### Why This Split
[Leo explains reasoning]
```

**AI role:** Review Leo's testing strategy; suggest improvements.

---

## Phase 4: REST API Design & Implementation (1 week)

### Learning Objectives
By end of Phase 4, Leo will understand:
- REST principles (resources, verbs, status codes)
- Proper HTTP semantics
- Error handling and exception mapping
- Request/response DTOs
- API versioning

### What Leo Will Learn (Concepts)
1. **Resources vs Operations:** GET /sessions, not /getSessionsByStudent
2. **HTTP Verbs:** GET (safe, idempotent), POST (not idempotent), PUT, DELETE
3. **Status Codes:** 200 OK, 201 Created, 400 Bad Request, 404 Not Found, 409 Conflict
4. **Idempotency:** What makes an operation safe to retry?
5. **Error Responses:** Structured error format

### Deliverables

#### 4.1 — API Specification (On Paper)

**Leo designs:** Before coding, write endpoints on paper:

```
POST /api/v1/sessions
  Request: { studentId, startTime, durationMinutes, tutorId }
  Response: 201 Created, { id, studentId, startTime, status, ... }
  Error: 409 Conflict if time overlaps, { error: "Session time unavailable" }
  Error: 404 Not Found if student doesn't exist

GET /api/v1/sessions/{id}
  Response: 200 OK, { id, studentId, startTime, ... }
  Error: 404 Not Found if session doesn't exist

DELETE /api/v1/sessions/{id}
  Response: 204 No Content
  Error: 404 Not Found, 400 Bad Request if can't cancel

GET /api/v1/students/{id}/sessions
  Response: 200 OK, [ { id, startTime, ... }, ... ]
```

**Leo must decide:**
- What is a resource? (Session is a resource, not "book a session operation")
- What status code for each error?
- What fields in request/response?
- How to version API?

**AI role:** Explain REST principles; Leo designs API.

#### 4.2 — DTOs (Data Transfer Objects)

**Leo writes:** Request and response objects:

```java
// src/main/java/com/tutorflow/dto/BookSessionRequest.java
public class BookSessionRequest {
  private String studentId;
  private LocalDateTime startTime;
  private int durationMinutes;
  private String tutorId;
  
  // Getters, setters, validation
}

// src/main/java/com/tutorflow/dto/SessionResponse.java
public class SessionResponse {
  private String id;
  private String studentId;
  private LocalDateTime startTime;
  private int durationMinutes;
  private String status;
  
  // Constructor from domain Session object
  public SessionResponse(Session session) {
    this.id = session.getId();
    this.studentId = session.getStudentId();
    // ...
  }
}
```

**Leo must understand:**
- Why DTOs exist (separate API contract from domain model)
- How to convert between domain entities and DTOs
- What fields should be in API response (not all internal fields)

**AI role:** Explain DTO pattern; Leo writes DTOs.

#### 4.3 — REST Controllers

**Leo writes:** Spring controllers that expose endpoints:

```java
// src/main/java/com/tutorflow/controller/SessionController.java
@RestController
@RequestMapping("/api/v1/sessions")
public class SessionController {
  
  private final SessionService sessionService;
  
  public SessionController(SessionService sessionService) {
    this.sessionService = sessionService;
  }
  
  @PostMapping
  public ResponseEntity<SessionResponse> bookSession(@RequestBody BookSessionRequest request) {
    // Leo implements:
    // 1. Call sessionService.bookSession(...)
    // 2. Catch exceptions and map to HTTP status
    // 3. Return 201 Created with response
  }
  
  @GetMapping("/{id}")
  public ResponseEntity<SessionResponse> getSession(@PathVariable String id) {
    // Leo implements retrieval
  }
  
  @DeleteMapping("/{id}")
  public ResponseEntity<Void> cancelSession(@PathVariable String id) {
    // Leo implements cancellation
  }
}
```

**Leo must:**
- Use correct HTTP methods and status codes
- Handle exceptions properly
- Map domain exceptions to HTTP responses
- Use path variables, query params, request bodies correctly

**AI role:** Explain Spring annotations; Leo writes controllers.

#### 4.4 — Exception Mapping

**Leo writes:** Global exception handler:

```java
// src/main/java/com/tutorflow/controller/GlobalExceptionHandler.java
@RestControllerAdvice
public class GlobalExceptionHandler {
  
  @ExceptionHandler(SessionOverlapException.class)
  public ResponseEntity<ErrorResponse> handleSessionOverlap(SessionOverlapException e) {
    return ResponseEntity.status(409).body(
      new ErrorResponse("Session time unavailable", e.getMessage())
    );
  }
  
  @ExceptionHandler(StudentNotFoundException.class)
  public ResponseEntity<ErrorResponse> handleNotFound(StudentNotFoundException e) {
    return ResponseEntity.status(404).body(
      new ErrorResponse("Student not found", e.getMessage())
    );
  }
}
```

**Leo must:**
- Define custom exceptions for business rules
- Map each exception to appropriate HTTP status
- Provide consistent error response format

**AI role:** Explain exception handling; Leo implements handlers.

#### 4.5 — API Tests (Controller Tests)

**Leo writes:** Tests in `src/test/java/com/tutorflow/controller/SessionControllerTest.java`

```java
@WebMvcTest(SessionController.class)
public class SessionControllerTest {
  
  @Autowired
  MockMvc mockMvc;
  
  @MockBean
  SessionService sessionService;
  
  @Test
  void bookSessionReturns201Created() throws Exception {
    // Arrange
    Session session = new Session(...);
    when(sessionService.bookSession(...)).thenReturn(session);
    
    // Act & Assert
    mockMvc.perform(post("/api/v1/sessions")
      .contentType(MediaType.APPLICATION_JSON)
      .content("""
        {
          "studentId": "123",
          "startTime": "2026-06-29T10:00:00",
          "durationMinutes": 60,
          "tutorId": "tutor1"
        }
      """))
      .andExpect(status().isCreated())
      .andExpect(jsonPath("$.id").exists());
  }
}
```

**Leo must:**
- Test HTTP status codes
- Test response body format
- Test error cases (409, 404, etc.)
- Use MockMvc to verify HTTP contract

**AI role:** Explain MockMvc; Leo writes API tests.

#### 4.6 — Documentation: `docs/API_DESIGN.md`

**Leo writes:**
- All endpoints (method, path, request, response, status codes)
- Error codes and their meanings
- Example requests/responses
- Error scenarios

**Example:**
```markdown
## POST /api/v1/sessions

Book a new session for a student.

### Request
```json
{
  "studentId": "student-123",
  "startTime": "2026-06-29T10:00:00",
  "durationMinutes": 60,
  "tutorId": "tutor-1"
}
```

### Response (201 Created)
```json
{
  "id": "session-456",
  "studentId": "student-123",
  "startTime": "2026-06-29T10:00:00",
  "durationMinutes": 60,
  "status": "BOOKED"
}
```

### Error Cases
- **409 Conflict:** Session time overlaps with existing session
- **404 Not Found:** Student or tutor doesn't exist
- **400 Bad Request:** Invalid input (e.g., duration <= 0)
```

**AI role:** Review Leo's documentation; suggest clarity improvements.

---

## Phase 5: Async & Resilience (1 week)

### Learning Objectives
By end of Phase 5, Leo will understand:
- When and why to use async
- Idempotency and retries
- Failure modes and recovery
- Spring @Async and @Retryable
- Observability (logging)

### What Leo Will Learn (Concepts)
1. **Async Processing:** Fire-and-forget operations (send email, notifications)
2. **Idempotency:** Safe to retry without side effects
3. **Retries:** Exponential backoff, max attempts
4. **Circuit Breaker:** Stop calling failing service, fail fast
5. **Observability:** Logging, tracing what happened

### Deliverables

#### 5.1 — Async Email Notifications

**Leo writes:** Async service for sending emails:

```java
// src/main/java/com/tutorflow/service/NotificationService.java
@Service
public class NotificationService {
  
  private static final Logger logger = LoggerFactory.getLogger(NotificationService.class);
  
  @Async
  public void sendSessionReminderEmail(Session session) {
    // Leo implements:
    // 1. Load student and tutor details
    // 2. Compose email
    // 3. Send via mail service
    // 4. Log result
    // 5. If fails, exception is logged (not blocking)
  }
}
```

**Leo must decide:**
- What is async? (doesn't block caller)
- What happens if email fails? (currently, just logged)
- Why send reminders async? (API shouldn't wait for email)

**AI role:** Explain @Async; Leo implements async methods.

#### 5.2 — Retry Logic

**Leo writes:** Retry configuration for resilient operations:

```java
// src/main/java/com/tutorflow/service/PaymentService.java
@Service
public class PaymentService {
  
  @Retryable(
    maxAttempts = 3,
    backoff = @Backoff(delay = 1000, multiplier = 2.0)
  )
  public PaymentResponse processPayment(Payment payment) {
    // Leo implements:
    // 1. Call external payment API
    // 2. If fails, Spring will retry up to 3 times
    // 3. Backoff: 1s, then 2s, then 4s
    
    // Must be IDEMPOTENT: safe to call multiple times
  }
  
  @Recover
  public PaymentResponse recoverPaymentFailure(
    IOException e, Payment payment
  ) {
    // Leo implements fallback if all retries fail
  }
}
```

**Leo must understand:**
- Idempotency: If you process payment 3 times, charge customer once
- Backoff strategy: Why wait longer each attempt?
- When retry helps (transient failures), when it doesn't

**AI role:** Explain retry patterns; Leo implements with Spring @Retryable.

#### 5.3 — Idempotency Keys

**Leo writes:** Ensure operations are safe to retry:

```java
@PostMapping("/api/v1/sessions")
public ResponseEntity<SessionResponse> bookSession(
  @RequestBody BookSessionRequest request,
  @RequestHeader("Idempotency-Key") String idempotencyKey
) {
  // Leo implements:
  // 1. Check if idempotencyKey already processed
  // 2. If yes, return cached response
  // 3. If no, process and cache result
}
```

**Leo must understand:**
- Why clients send idempotency keys
- How to cache and retrieve by key
- When booking is safe to retry

**AI role:** Explain idempotency; Leo implements checking logic.

#### 5.4 — Logging & Observability

**Leo writes:** Structured logging throughout:

```java
@Service
public class SessionService {
  
  private static final Logger logger = LoggerFactory.getLogger(SessionService.class);
  
  public Session bookSession(String studentId, LocalDateTime startTime, int durationMinutes, String tutorId) {
    logger.info("Attempting to book session for student: {} at {}", studentId, startTime);
    
    try {
      Student student = studentRepository.findById(studentId);
      // ...
      Session session = new Session(...);
      sessionRepository.save(session);
      
      logger.info("Session booked successfully: {}", session.getId());
      return session;
    } catch (SessionOverlapException e) {
      logger.warn("Session overlap for student {}: {}", studentId, e.getMessage());
      throw e;
    }
  }
}
```

**Leo must:**
- Log at appropriate levels (INFO, WARN, ERROR)
- Include context (IDs, timestamps)
- Avoid logging sensitive data (passwords, payment info)

**AI role:** Explain logging best practices; Leo adds logging.

#### 5.5 — Documentation: `docs/ASYNC_STRATEGY.md`

**Leo writes:**
- What operations are async and why
- Failure modes and recovery
- Retry strategy and backoff
- Idempotency approach
- How to monitor failures

---

## Phase 6: Deployment & DevOps (1 week)

### Learning Objectives
By end of Phase 6, Leo will understand:
- Docker best practices
- 12 Factor App methodology
- Environment configuration
- Health checks and observability
- Local development vs production

### What Leo Will Learn (Concepts)
1. **Docker:** Containerization, layers, build optimization
2. **12 Factor:** Stateless processes, environment config, logs
3. **Docker Compose:** Multi-container local development
4. **Health Checks:** Liveness and readiness probes
5. **Security:** Secrets management, non-root user

### Deliverables

#### 6.1 — Dockerfile

**Leo writes:** `Dockerfile` with explanation:

```dockerfile
# Multi-stage build: compile + runtime
FROM eclipse-temurin:21-jdk-alpine AS build
WORKDIR /build

# Copy source
COPY pom.xml .
COPY src ./src

# Build
RUN mvn clean package -DskipTests

# Runtime stage: Alpine is small ~90MB vs Ubuntu 300MB+
FROM eclipse-temurin:21-jre-alpine
WORKDIR /app

# Security: run as non-root user
RUN addgroup --gid 1001 tutorflow && \
    adduser --uid 1001 --ingroup tutorflow tutorflow

# Copy built JAR from build stage
COPY --from=build /build/target/tutorflow.jar .

# Switch to non-root user
USER tutorflow:tutorflow

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=10s --retries=3 \
  CMD java -cp app.jar \
    -Dspring.boot.application-type=basic \
    org.springframework.boot.loader.JarLauncher health

# Default port
EXPOSE 8080

# Run app
ENTRYPOINT ["java", "-jar", "app.jar"]
```

**Leo must understand:**
- Multi-stage builds (smaller final image)
- Alpine vs Ubuntu (smaller but less tools)
- Non-root user (security)
- Health check (container orchestration)

**AI role:** Explain Docker concepts; Leo writes Dockerfile with comments explaining each section.

#### 6.2 — Docker Compose for Local Development

**Leo writes:** `infra/docker-compose.yml`:

```yaml
version: '3.8'

services:
  app:
    build: ..
    ports:
      - "8080:8080"
    environment:
      SPRING_DATASOURCE_URL: jdbc:postgresql://postgres:5432/tutorflow
      SPRING_DATASOURCE_USERNAME: tutorflow_user
      SPRING_DATASOURCE_PASSWORD: dev_password_change_me
      SPRING_JPA_HIBERNATE_DDL_AUTO: validate
      SPRING_PROFILES_ACTIVE: dev
    depends_on:
      postgres:
        condition: service_healthy
    networks:
      - tutorflow

  postgres:
    image: postgres:15-alpine
    ports:
      - "5432:5432"
    environment:
      POSTGRES_DB: tutorflow
      POSTGRES_USER: tutorflow_user
      POSTGRES_PASSWORD: dev_password_change_me
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U tutorflow_user"]
      interval: 10s
      timeout: 5s
      retries: 5
    networks:
      - tutorflow

volumes:
  postgres_data:

networks:
  tutorflow:
    driver: bridge
```

**Leo must understand:**
- Services (app, database)
- Environment variables (connection strings, credentials)
- depends_on with health check (app waits for DB)
- Volumes (persist database between restarts)
- Networks (inter-service communication)

**AI role:** Explain docker-compose; Leo writes config with comments.

#### 6.3 — Application Properties

**Leo writes:** `src/main/resources/application.yml`:

```yaml
spring:
  application:
    name: tutorflow
  profiles:
    active: dev
  datasource:
    url: ${SPRING_DATASOURCE_URL}
    username: ${SPRING_DATASOURCE_USERNAME}
    password: ${SPRING_DATASOURCE_PASSWORD}
  jpa:
    hibernate:
      ddl-auto: validate  # Never auto-create schema in prod
    show-sql: false
    properties:
      hibernate:
        format_sql: true
  flyway:
    enabled: true
    locations: classpath:db/migration

server:
  port: 8080
  servlet:
    context-path: /

logging:
  level:
    root: INFO
    com.tutorflow: DEBUG
```

**Leo must understand:**
- 12 Factor: Config from environment, not hardcoded
- Different profiles (dev, test, prod)
- Why validate (not auto-create) schema in production

**AI role:** Explain 12 Factor; Leo writes app config.

#### 6.4 — Health Check Endpoint

**Leo writes:** Actuator health endpoint:

```java
// src/main/java/com/tutorflow/controller/HealthController.java
@RestController
@RequestMapping("/")
public class HealthController {
  
  @Autowired
  private HealthIndicator healthIndicator;
  
  @GetMapping("/health")
  public ResponseEntity<HealthResponse> health() {
    // Leo implements:
    // 1. Check database connectivity
    // 2. Check external services (payment API, email)
    // 3. Return UP or DOWN
    
    return ResponseEntity.ok(new HealthResponse("UP"));
  }
  
  @GetMapping("/ready")
  public ResponseEntity<String> readiness() {
    // Ready to accept traffic?
    return ResponseEntity.ok("Ready");
  }
}
```

**Leo must understand:**
- Liveness (is app running?)
- Readiness (is app ready to handle requests?)
- Container orchestration uses these

**AI role:** Explain health checks; Leo implements.

#### 6.5 — Deployment Documentation

**Leo writes:** `docs/DEPLOYMENT.md`:

```markdown
## Local Development

1. Start containers: `docker-compose up`
2. App runs on http://localhost:8080
3. Database on localhost:5432

## Production Deployment

### Requirements
- Docker runtime
- Environment variables configured (database credentials, API keys)
- Reverse proxy (nginx) for HTTPS

### Build & Deploy
```bash
docker build -t tutorflow:latest .
docker run \
  -e SPRING_DATASOURCE_URL=jdbc:postgresql://prod-db:5432/tutorflow \
  -e SPRING_DATASOURCE_USERNAME=prod_user \
  -e SPRING_DATASOURCE_PASSWORD=<secret> \
  -p 8080:8080 \
  tutorflow:latest
```

### Monitoring
- Health: GET /health
- Readiness: GET /ready
- Logs: `docker logs <container>`
```

**Leo must write:**
- How to run locally
- How to build for production
- Environment variables needed
- Monitoring and troubleshooting

---

## Final Deliverables & Acceptance Criteria

### By End of Project, Leo Should Have:

1. **Code Repository (GitHub)**
   - Well-organized structure
   - Meaningful commit history (not 1 giant commit)
   - README with setup instructions

2. **Running Application**
   - `docker-compose up` starts everything
   - API endpoints work
   - All tests pass
   - 80%+ test coverage

3. **Documentation**
   - README
   - docs/DATABASE_DESIGN.md
   - docs/DOMAIN_DESIGN.md
   - docs/TESTING_STRATEGY.md
   - docs/API_DESIGN.md
   - docs/ASYNC_STRATEGY.md
   - docs/DEPLOYMENT.md
   - docs/ARCHITECTURE_OVERVIEW.md

4. **Understanding (This is the Real Win)**
   - Can explain every design decision
   - Can modify requirements (e.g., "add student groups") and redesign
   - Can debug issues without copy-pasting Stack Overflow
   - Can teach someone else how it works
   - Can pass an interview discussing this project

### What Constitutes Success

**Weak:** "I built a project"  
**Strong:** "I built a project and can explain the architecture, tradeoffs, and why I made each decision"  
**Excellent:** "I built a project, can debug it, can modify it, and can teach someone else"

---

## Project Structure (Leo Creates This)

```
tutorflow/
├── README.md
├── pom.xml                         # Maven build
├── src/
│   ├── main/
│   │   ├── java/com/tutorflow/
│   │   │   ├── TutorFlowApplication.java
│   │   │   ├── config/             # Spring config
│   │   │   ├── domain/             # Domain entities (Student, Session, etc.)
│   │   │   ├── dto/                # Request/response DTOs
│   │   │   ├── repository/         # Repository interfaces
│   │   │   ├── service/            # Business logic services
│   │   │   ├── controller/         # REST controllers
│   │   │   └── exception/          # Custom exceptions
│   │   └── resources/
│   │       ├── application.yml
│   │       ├── application-dev.yml
│   │       └── db/migration/       # Flyway migrations (V001__, V002__, etc.)
│   └── test/
│       ├── java/com/tutorflow/
│       │   ├── domain/             # Domain entity tests
│       │   ├── service/            # Service layer tests
│       │   ├── controller/         # API controller tests
│       │   ├── integration/        # Integration tests
│       │   └── testdata/           # Test builders and fixtures
│       └── resources/
│           └── application-test.yml
├── infra/
│   └── docker-compose.yml
├── Dockerfile
└── docs/
    ├── DATABASE_DESIGN.md
    ├── DOMAIN_DESIGN.md
    ├── TESTING_STRATEGY.md
    ├── API_DESIGN.md
    ├── ASYNC_STRATEGY.md
    ├── DEPLOYMENT.md
    └── ARCHITECTURE_OVERVIEW.md
```

---

## Timeline & Milestones

| Week | Phase | Deliverable | AI Role |
|------|-------|-------------|---------|
| 1 | Database | Flyway migrations + docs/DATABASE_DESIGN.md | Explain, review |
| 2 | Domain | Domain entities, services, tests + docs/DOMAIN_DESIGN.md | Explain, code review |
| 3 | Testing | Unit + integration tests, 80%+ coverage + docs/TESTING_STRATEGY.md | Explain, review test quality |
| 4 | API | REST controllers, DTOs, error handling + docs/API_DESIGN.md | Explain, code review |
| 5 | Async | Async services, retry logic + docs/ASYNC_STRATEGY.md | Explain patterns, code review |
| 6 | DevOps | Docker, compose, health checks + docs/DEPLOYMENT.md | Explain best practices, review |

**Total:** 4-6 weeks, 20-30 hours learning + 30-40 hours coding

---

## Key Principles

### 1. Leo Writes All The Code
AI can explain, scaffold, review. Leo implements.

### 2. Documentation Is Part Of Learning
Writing `docs/DATABASE_DESIGN.md` forces Leo to think deeply.

### 3. Commits Should Tell A Story
Not "final commit" with 10k lines. Each commit should be logical step.

### 4. Tests Are Documentation
A good test shows how to use the code.

### 5. No Copy-Paste From Stack Overflow
AI can explain patterns; Leo adapts and understands.

---

## Questions Leo Should Be Able To Answer

By end of project:

1. "Why is Student a separate aggregate from Session?"
2. "What happens if booking fails after generating invoice?"
3. "How do you detect overlapping sessions?"
4. "Why use Testcontainers instead of mocking the database?"
5. "Why is status code 409 better than 400 for overlapping sessions?"
6. "How does Spring @Async work? What happens if email fails?"
7. "What does Flyway migration do? Why version control schema?"
8. "How would you add a new feature (e.g., group sessions)? Where does code go?"

If Leo can answer these, he learned.

---

## Resume Bullet After Project

> **Architected TutorFlow**, a Spring Boot backend platform for tutoring operations, demonstrating proficiency in database design (normalization, constraints, Flyway migrations), domain-driven design with aggregate patterns, testing pyramid (unit + integration with Testcontainers, 80%+ coverage), REST API principles with proper HTTP semantics, async resilience patterns (Spring @Async, @Retryable with exponential backoff), and containerized deployment (Docker, docker-compose, health checks). Documented all design decisions across 6 architectural domains.

---

## How To Use This Document

**For Leo:** This is your spec. Follow the phases, write the code, document your decisions.

**For AI sessions helping Leo:** Read the "CRITICAL NOTE FOR AI SESSIONS" at the top. Don't write code for Leo. Explain, scaffold, review, help debug. The learning is the point.

**For Leo's future self / job interviewers:** This project shows depth, not just "I built something." It's proof of systematic thinking and willingness to learn.

---

## GitHub Repository Setup

This document should live in the repo at: `docs/DESIGN_DOCUMENT.md`

**Repository Structure:**
```
tutorflow/
├── README.md                       # Project overview, quick start
├── docs/
│   ├── DESIGN_DOCUMENT.md         # This file (master reference)
│   ├── DATABASE_DESIGN.md         # Leo writes: database decisions
│   ├── DOMAIN_DESIGN.md           # Leo writes: DDD & entities
│   ├── TESTING_STRATEGY.md        # Leo writes: testing approach
│   ├── API_DESIGN.md              # Leo writes: REST API spec
│   ├── ASYNC_STRATEGY.md          # Leo writes: async & resilience
│   ├── DEPLOYMENT.md              # Leo writes: Docker & ops
│   └── ARCHITECTURE_OVERVIEW.md   # Leo writes: system overview
├── pom.xml
├── Dockerfile
├── infra/
│   └── docker-compose.yml
├── src/
│   ├── main/java/com/tutorflow/
│   └── test/java/com/tutorflow/
└── .gitignore
```

**How Other AIs Should Use This:**
1. Read "QUICK REFERENCE" section (above)
2. Read "CRITICAL NOTE FOR AI SESSIONS" section
3. Find which phase Leo is asking about
4. Guide him through deliverables, but don't do the work

---

End of Design Document. Last updated: 2026-06-29
