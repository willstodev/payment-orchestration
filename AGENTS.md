# AGENTS.md

## Mission

This repository uses AI-assisted software development.

AI agents are engineering collaborators.

Their purpose is to accelerate:

- implementation
- investigation
- testing
- debugging
- refactoring
- documentation
- repository navigation
- tool execution

They must not replace engineering ownership.

The developer remains responsible for requirements, architecture, trade-offs, and final decisions.

---

## Core Principle

> Understand before implementing.

Do not generate large amounts of code from vague requirements.

Before making a meaningful change:

1. understand the task
2. inspect the relevant repository context
3. identify expected behavior
4. identify risks and edge cases
5. determine the smallest appropriate change
6. implement
7. verify
8. explain the result

---

## Project Development Model

This project evolves incrementally.

Do not attempt to build the final architecture early.

The preferred progression is:

```text
requirements
→ minimal implementation
→ tests
→ working behavior
→ new problem discovered
→ architectural evolution
```

Complexity should emerge from real requirements.

---

## MVP First

The project begins as a small MVP.

When implementing a task:

- solve the current requirement
- preserve room for reasonable future evolution
- avoid implementing speculative future features
- avoid creating abstractions for hypothetical use cases

Do not implement future roadmap stages unless explicitly requested.

Design for change without implementing change prematurely.

---

## Scope Control

Prefer small, reviewable changes.

A single task should have a clear purpose.

Do not silently:

- rewrite unrelated code
- restructure large parts of the project
- introduce infrastructure
- add architectural patterns
- change public behavior
- modify unrelated tests

If a larger change appears necessary, explain why before proceeding.

---

## Before Writing Code

For non-trivial work, establish:

### Behavior

- What should happen?
- What should not happen?

### Inputs

- What enters the system?
- What validation is required?

### Outputs

- What should the caller receive?

### Failure

- What can fail?
- How should the system react?

### Invariants

- What must always remain true?

### Verification

- What tests prove the required behavior?

If these are unclear and materially affect implementation, surface the ambiguity.

Do not silently invent business rules.

---

## Architecture

Prefer the simplest architecture that satisfies current requirements.

Architecture should optimize for:

- clarity
- correctness
- maintainability
- testability
- explicit boundaries

Avoid architecture driven by trend or portfolio appearance.

---

## Architectural Complexity

Do not introduce the following without a concrete requirement:

- microservices
- Kafka
- RabbitMQ
- Redis
- Valkey
- Kubernetes
- distributed caching
- CQRS
- event sourcing
- service mesh
- complex domain frameworks
- custom infrastructure abstractions

If one becomes appropriate, explain:

1. the existing problem
2. why the current solution is insufficient
3. possible alternatives
4. why the proposed solution is appropriate
5. what new complexity it introduces

---

## Java

Use modern Java practices appropriate for the configured JDK.

Prefer:

- explicit types
- meaningful domain names
- small focused classes
- immutability where appropriate
- constructor injection
- clear responsibility boundaries
- standard JDK functionality where sufficient

Avoid:

- unnecessary inheritance
- clever generic abstractions
- large utility classes
- hidden side effects
- unnecessary reflection
- framework magic without justification

Code should favor readability over cleverness.

---

## Spring Boot

Spring Boot is infrastructure around the application, not the domain itself.

Prefer:

- thin controllers
- explicit application services
- clear domain behavior
- constructor injection
- explicit configuration
- understandable dependency boundaries

Avoid putting business logic directly inside:

- controllers
- repository implementations
- configuration classes
- DTO mapping logic

Do not create layers simply because a tutorial or template contains them.

Every layer should have a responsibility.

---

## Payment Domain

Payment systems require strict correctness.

Pay special attention to:

- monetary values
- currency
- payment identity
- state transitions
- duplicate operations
- transaction boundaries
- concurrency
- retries
- provider communication
- asynchronous updates

Never use floating-point types such as:

```text
float
double
```

for monetary amounts.

Prefer appropriate monetary representations such as `BigDecimal` combined with explicit currency semantics when relevant.

Business invariants should be explicit and testable.

---

## State Transitions

Payment state should not change arbitrarily.

When state transitions are introduced:

- define valid transitions
- reject invalid transitions
- test important transitions
- consider duplicate external notifications
- consider delayed notifications

Do not allow infrastructure events to mutate business state without defined rules.

---

## External Systems

Treat all external systems as unreliable.

Assume that:

- requests may time out
- responses may arrive late
- responses may arrive twice
- requests may be processed despite a timeout
- providers may become unavailable
- payloads may be invalid
- provider behavior may change

Do not design network communication under an exactly-once assumption.

---

## Idempotency

Do not introduce idempotency mechanisms before they are required.

When they become necessary, consider:

- idempotency keys
- operation identity
- storage strategy
- request replay
- response replay
- race conditions
- expiration behavior

Idempotency should protect a defined business operation rather than exist as a generic technical feature.

---

## Persistence

When persistence is introduced, consider:

- database constraints
- transactions
- concurrency
- unique constraints
- indexes
- migration safety
- data integrity

Important invariants should be enforced at the strongest appropriate layer.

Do not depend only on application checks when the database can safely protect the invariant.

---

## Testing Philosophy

Tests are part of production code quality.

A feature is not complete because the happy path works.

Choose the smallest test capable of proving the required behavior.

Possible levels include:

- unit tests
- integration tests
- persistence tests
- HTTP tests
- provider integration tests
- contract tests

---

## Test Expectations

Important behavior should include appropriate coverage for:

- successful scenarios
- validation failures
- invalid states
- boundary conditions
- duplicate operations
- provider failures
- regression scenarios

Do not create tests solely to increase coverage metrics.

Test behavior that matters.

---

## Test Quality

Prefer behavior-oriented tests.

Tests should survive reasonable internal refactoring.

Avoid excessive mocking.

Mock external boundaries when appropriate.

When actual infrastructure behavior matters, prefer realistic integration tests.

Testcontainers may be introduced when database or infrastructure integration justifies it.

---

## Test Integrity

Never:

- delete valid tests simply because they fail
- weaken assertions to make a build pass
- change expected behavior without explaining why
- claim a test passed without running it

If implementation and tests disagree, investigate which behavior is correct.

---

## Verification

Before declaring a task complete:

1. inspect changed files
2. compile the project
3. run relevant tests
4. run the broader test suite when appropriate
5. confirm no unrelated behavior changed

Typical Maven commands may include:

```bash
mvn test
```

and later:

```bash
mvn verify
```

Do not claim successful verification unless the commands actually succeeded.

---

## Dependencies

Do not add dependencies casually.

Before introducing a dependency:

1. check whether the JDK already solves the problem
2. check whether an existing project dependency solves it
3. understand what the dependency adds
4. consider maintenance and operational impact

Prefer mature and widely adopted libraries.

New significant dependencies must be mentioned in the final change summary.

---

## Error Handling

Errors should be deliberate.

Prefer errors that are:

- explicit
- predictable
- meaningful
- observable

Do not silently swallow exceptions.

Do not expose implementation details or stack traces through public APIs.

Differentiate when appropriate between:

- validation errors
- business rule violations
- provider failures
- infrastructure failures
- unexpected internal failures

---

## Observability

Observability should evolve with operational needs.

Possible capabilities include:

- structured logs
- correlation IDs
- metrics
- distributed traces
- health checks

Do not add an observability stack before there is behavior worth observing.

When observability is introduced, it should answer concrete operational questions.

---

## Security

Never commit:

- passwords
- tokens
- API keys
- private keys
- cloud credentials
- secrets

Never log sensitive payment or credential information.

Use appropriate configuration and secret management mechanisms.

---

## AWS

AWS is part of the project's possible production evolution.

Do not introduce AWS services during early development unless explicitly requested.

When AWS becomes relevant, prefer managed services when they meaningfully reduce operational complexity.

Possible future services include:

- Amazon RDS
- Amazon SQS
- Amazon ECS
- Amazon ElastiCache
- Amazon CloudWatch
- AWS Secrets Manager

Each service must solve a defined problem.

---

## AI & MCP

AI tooling may evolve alongside the repository.

Potential integrations may include MCP servers or tools that provide access to:

- GitHub
- documentation
- databases
- CI systems
- AWS resources
- development tools

Tool access does not imply decision authority.

Always interpret tool output critically.

Do not modify external resources unless the task explicitly requires it.

---

## Repository Exploration

Before changing existing behavior:

- inspect relevant files
- inspect related tests
- inspect nearby conventions
- understand existing dependencies

Do not assume the repository structure from memory.

The repository is the source of truth for the current implementation.

---

## Documentation

Documentation should evolve with the project.

Do not generate extensive documentation for features that do not exist yet.

Update documentation when a change materially affects:

- architecture
- public APIs
- development setup
- system behavior
- operational assumptions

Architecture Decision Records may be introduced later when the project begins making meaningful architectural decisions.

---

## Refactoring

Refactoring should preserve externally observable behavior unless explicitly stated otherwise.

Prefer small refactors.

Avoid mixing:

- major refactoring
- new business behavior
- infrastructure changes

in the same task when they can reasonably be separated.

---

## Git

Keep changes focused.

Suggested commit style:

```text
feat: create payment
fix: prevent duplicate payment processing
test: cover invalid payment transition
refactor: isolate payment provider boundary
docs: explain payment lifecycle
chore: configure project tooling
```

Do not create commits unless explicitly requested.

Do not push unless explicitly requested.

---

## Change Summary

After completing implementation work, summarize:

### What changed

The concrete behavior or structure modified.

### Why

The requirement or engineering reason.

### Tests

What was added or executed.

### Risks

Anything that deserves developer attention.

### Follow-up

Possible next steps, without implementing them unless requested.

---

## When Unsure

Do not silently guess.

Follow this order:

1. inspect the repository
2. inspect existing tests
3. inspect existing conventions
4. identify the ambiguity
5. consider alternatives
6. explain the trade-off

Ask for developer input when the decision materially affects:

- product behavior
- domain rules
- architecture
- infrastructure
- security
- public API contracts

---

## Definition of Done

A task is complete when:

- the requested behavior exists
- relevant tests exist
- relevant tests pass
- the project builds
- the implementation is understandable
- no unnecessary complexity was introduced
- documentation was updated when necessary
- important assumptions are explicit

Working code is required.

Understanding the code is equally important.

---

## Final Rule

> AI should make the developer faster, not less aware of the system.

Prefer a small change that is fully understood over a large change that merely appears complete.