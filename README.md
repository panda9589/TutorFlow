# TutorFlow

A learning-first backend platform for tutoring business operations. Built with Java 21, Spring Boot 3.x, PostgreSQL, and Docker.

**This is an educational project.** The goal is to learn deep backend engineering concepts (database design, DDD, testing, REST, async, DevOps) through building a real system.

## Quick Start

### For Leo (Project Owner)

1. **Read the design document first:**
   ```
   docs/DESIGN_DOCUMENT.md
   ```
   This explains what you're building and what you must learn yourself.

2. **Follow the 6 phases:**
   - Phase 1: Database design (1 week)
   - Phase 2: Domain model & services (1 week)
   - Phase 3: Testing (1 week)
   - Phase 4: REST API (1 week)
   - Phase 5: Async & resilience (1 week)
   - Phase 6: DevOps & deployment (1 week)

3. **For each phase:**
   - Read the learning objectives
   - Complete the deliverables
   - Write the documentation
   - Commit to git

### For AI Sessions Helping Leo

**START HERE:** Read the "CRITICAL NOTE FOR AI SESSIONS" section in `docs/DESIGN_DOCUMENT.md`.

In short:
- **Don't write code for Leo.** Explain, scaffold, review.
- **Make him think.** Ask clarifying questions, point to errors, make him fix them.
- **Test his learning.** Ask him to explain decisions, modify design, teach it back.

## Project Structure

```
tutorflow/
├── README.md                          # This file
├── docs/
│   ├── DESIGN_DOCUMENT.md            # Master design spec (read first!)
│   ├── DATABASE_DESIGN.md            # Phase 1: Leo's database decisions
│   ├── DOMAIN_DESIGN.md              # Phase 2: Leo's DDD approach
│   ├── TESTING_STRATEGY.md           # Phase 3: Leo's testing pyramid
│   ├── API_DESIGN.md                 # Phase 4: Leo's REST API spec
│   ├── ASYNC_STRATEGY.md             # Phase 5: Leo's async patterns
│   ├── DEPLOYMENT.md                 # Phase 6: Leo's ops decisions
│   └── ARCHITECTURE_OVERVIEW.md      # Final: System-wide overview
├── pom.xml                           # Maven configuration
├── Dockerfile                        # Production container
├── infra/
│   └── docker-compose.yml            # Local development stack
└── src/
    ├── main/java/com/tutorflow/      # Application code
    └── test/java/com/tutorflow/      # Test code
```

## Business Context

Leo runs a tutoring business for Ontario Grade 7–12 students. TutorFlow is a backend platform to:
- Manage student profiles and sessions
- Detect and prevent booking conflicts
- Process payments and generate invoices
- Track student progress
- Send session reminders

## Technical Stack

- **Language:** Java 21
- **Framework:** Spring Boot 3.x
- **Database:** PostgreSQL 15+
- **Build:** Maven
- **Containerization:** Docker + docker-compose
- **Quality Target:** 80%+ test coverage, clean code

## Learning Outcomes

By the end of this project, Leo will understand:

1. **Database Design:** Normalization, constraints, migrations, indices
2. **Domain-Driven Design:** Entities, value objects, aggregates, services
3. **Testing:** Unit, integration, test fixtures, meaningful coverage
4. **REST API:** HTTP semantics, status codes, error handling
5. **Async Patterns:** Fire-and-forget, retries, idempotency, resilience
6. **DevOps:** Docker, docker-compose, health checks, 12 Factor principles

## Important Principles

1. **Leo writes all code.** AI guides, reviews, explains — doesn't code for him.
2. **Documentation is learning.** Writing `docs/DATABASE_DESIGN.md` forces understanding.
3. **Tests prove learning.** A good test shows how code works and why it's correct.
4. **No Stack Overflow copy-paste.** Adapt and understand, don't just copy.

## How to Verify Learning

After each phase, Leo should be able to:
- Explain every design decision
- Modify requirements and redesign
- Teach someone else how it works
- Debug issues independently

If he can't do these, he didn't learn — he just coded.

## Resume Value

After completion, Leo can say:

> "I architected TutorFlow, a Spring Boot backend for tutoring operations. Demonstrated proficiency in database design (normalization, Flyway migrations), domain-driven design (aggregates, services), testing pyramid (unit + integration with Testcontainers, 80%+ coverage), REST API principles (HTTP semantics, proper status codes), async resilience (Spring @Async, @Retryable with exponential backoff), and containerized deployment (Docker, docker-compose). Documented all design decisions across 6 architectural domains."

This isn't just "I built something." This is proof of systematic thinking and deep understanding.

## Next Steps

1. Clone the repo
2. Read `docs/DESIGN_DOCUMENT.md`
3. Start Phase 1 (database design on paper)
4. Create first Flyway migration
5. Commit and push

---

**Last updated:** 2026-06-29  
**Owner:** Leo / Yingbo Tong  
**License:** Private (Leo's portfolio project)
