# Enterprise Development Constitution (Generic)

This Constitution establishes the immutable principles and best practices for all software development. It serves as the supreme governing document, ensuring that all specifications, plans, and implementations adhere to a common standard of quality, security, and maintainability. This document is the ultimate source of truth, and all AI-driven and human-led development must comply with its articles.

## Core Principles

### I. Specification-First (The Source of Truth)

All development work begins with a specification. The specification is an executable artifact that defines the *what* and the *why*, not the *how*. Code is the generated output of a specification and its implementation plan; it is not the primary artifact. Evolving the software means evolving the specification.

### II. Test-First (Non-Negotiable)

All implementation **MUST** follow a strict Test-Driven Development (TDD) workflow. No implementation code shall be written before:

1. Contracts (API specifications, data models) are defined.
2. Tests that validate these contracts are written and approved.
3. The tests are confirmed to **FAIL** (Red phase) before implementation begins.
4. Implementation code is then written to make the tests pass (Green phase).

### III. Simplicity and YAGNI (You Ain't Gonna Need It)

Start with the simplest possible solution that meets the requirements. Avoid speculative features, over-engineering, and premature abstractions. All complexity must be explicitly justified against a current, concrete requirement.

### IV. API-First Design

All services **MUST** expose their functionality through well-defined, contract-first APIs (e.g., OpenAPI for REST, gRPC for RPC). This contract is the source of truth for service interactions and is used to generate client code, tests, and documentation.

### V. Observability by Design

Systems must be designed to be observable from the outset. This is not an afterthought.

- **Structured Logging:** All logs must be structured (e.g., JSON) and include a correlation ID that tracks a request across all services.
- **Metrics:** Services must expose key performance indicators (e.g., latency, error rate, throughput) in a standardized format (e.g., Prometheus).
- **Distributed Tracing:** All services must participate in distributed tracing to enable end-to-end request analysis.

---

## General Coding Best Practices

### VI. Code Quality and Maintainability

- **Framework & Language Versioning:** All projects **MUST** target a recent, stable Long-Term Support (LTS) version of their respective language and framework.
- **Code Readability and Consistency:** Code **MUST** be clean, readable, and self-documenting. Projects **MUST** enforce a consistent style using automated linters and formatters.
- **Asynchronous Programming:** All I/O-bound operations (e.g., network calls, database queries) **MUST** be implemented asynchronously to prevent blocking execution threads. Cancellation mechanisms (e.g., tokens, signals) must be propagated.
- **Dependency Injection:** The application **MUST** be built on the principle of Dependency Injection. Components should be loosely coupled and resolved via constructor injection. Avoid static accessors and the Service Locator pattern.
- **Robust Error Handling:** Errors and exceptions **MUST NOT** be silently ignored. Implement centralized error handling middleware to catch unhandled exceptions and return meaningful, standardized error responses.

---

## Database Best Practices

### VII. Schema and Data Integrity

- **Migrations:** Database schema changes **MUST** be managed through a code-first, migrations-based approach (e.g., EF Core, Alembic, Flyway). All migrations must be reversible.
- **Data Access:** Do not use raw SQL strings in application code. All data access **MUST** be performed through an Object-Relational Mapper (ORM) or a type-safe query builder.
- **Querying:** Proactively prevent performance issues. Avoid N+1 query problems by using explicit data loading strategies (e.g., joins, eager loading). All data access methods must be asynchronous.
- **Transactions:** Business operations that involve multiple database writes **MUST** be wrapped in an explicit transaction to ensure atomicity.
- **Indexing:** All foreign key columns and columns frequently used in query predicates (`WHERE` clauses) **MUST** be indexed.

---

## OOP/SOLID Principles

### VIII. Code Structure and Design

- **Single Responsibility Principle (SRP):** Every class, method, or module must have one, and only one, reason to change. Services should be small, focused, and cohesive.
- **Open/Closed Principle (OCP):** Entities should be open for extension but closed for modification. Use interfaces and abstraction to allow new functionality to be added without changing existing code.
- **Interface Segregation Principle (ISP):** Clients should not be forced to depend on interfaces they do not use. Prefer smaller, role-specific interfaces over large, general-purpose ones.
- **Dependency Inversion Principle (DIP):** High-level modules should not depend on low-level modules; both should depend on abstractions. Code **MUST** depend on interfaces, not concrete implementations.
- **Composition Over Inheritance:** Favor composition to achieve code reuse and flexibility. Inheritance should be used only when a true "is-a" relationship exists.

---

## Cloud-Native Best Practices

### IX. Architecture for the Cloud

- **Statelessness:** Services **MUST** be stateless. All persistent state must be externalized to a dedicated storage layer (e.g., database, cache, object store).
- **Configuration:** Application configuration **MUST** be externalized from the code and supplied via the environment (e.g., environment variables, Kubernetes ConfigMaps).
- **Secrets Management:** Secrets (passwords, API keys, connection strings) **MUST NOT** be stored in source code or configuration files. They must be injected at runtime from a secure store (e.g., HashiCorp Vault, Azure Key Vault, AWS Secrets Manager).
- **Containerization:** All applications and services **MUST** be packaged as Docker containers. Container images must be minimal, secure, and run as a non-root user.
- **Health Checks:** Every service **MUST** expose health check endpoints (e.g., `/health/live`, `/health/ready`) to enable automated health monitoring and graceful deployments by container orchestrators.

---

## Secure by Design

### X. Proactive Security and Developer Enablement

- **Principle of Least Privilege:** All identities (users, services) **MUST** be granted the minimum permissions required to perform their functions. Start with zero trust.
- **Authentication & Authorization:**
  - Production environments **MUST** use strong, centralized authentication (e.g., OIDC).
  - Authorization is **NOT** optional. All protected endpoints **MUST** enforce authorization policies.
  - For **local development and testing**, a simplified authentication handler **MUST** be used. This handler, enabled only in the `Development` environment, bypasses external identity providers and simulates an authenticated user with claims defined in a local configuration file (e.g., `.env.local`, `appsettings.Development.json`). This ensures that authorization logic is fully exercised during development without the friction of real authentication.
- **Data Protection:** All data **MUST** be encrypted in transit (TLS 1.2+). Sensitive data at rest **MUST** be encrypted in the database.
- **Input Validation:** All data originating from an external source (user input, API calls) **MUST** be validated and sanitized on arrival to prevent injection, cross-site scripting, and other payload-based attacks.
- **Dependency Security:** Third-party dependencies **MUST** be regularly scanned for known vulnerabilities using automated tooling (e.g., Dependabot, Snyk).

---

## Governance

- This Constitution is the highest authority on development practices and supersedes all other guidelines.
- Compliance with this Constitution will be automatically verified during specification, planning, and implementation phases.
- Any deviation from these articles requires a formal, documented exception that must be reviewed and approved by project maintainers. The justification for the deviation must be recorded in the `research.md` file for the relevant specification.

**Version**: 2.0.0 | **Ratified**: 2025-10-24 | **Last Amended**: 2025-10-24
