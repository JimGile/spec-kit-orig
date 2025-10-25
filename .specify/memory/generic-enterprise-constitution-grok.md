<!--
Sync Impact Report
==================
Version change: none → 1.0.0
Type: Initial creation (MINOR - new governance document)
Rationale: First formal constitution for enterprise development projects based on existing
generic-enterprise-constitution.md principles plus additional enterprise-focused principles.

Added sections:
- All core principles (10 principles total, expanded beyond template)
- Enterprise development workflow guidelines
- Quality gates and compliance section
- Governance rules

Templates requiring updates:
- ✅ plan-template.md (Constitution Check section already present)
- ✅ spec-template.md (Requirements section aligns with principle I)
- ✅ tasks-template.md (Task organization reflects principle VIII modularity)
- ✅ Commands (no agent-specific updates needed - generic guidance maintained)

Follow-up TODOs: None
-->

# Enterprise Development Constitution

## Core Principles

### I. Specification-First (The Source of Truth)

All development work begins with a specification. The specification is an executable artifact that defines the *what* and the *why*, not the *how*. Code is the generated output of a specification and its implementation plan; it is not the primary artifact. Evolving the software means evolving the specification.

**Rules**:

- Specifications MUST be written before any implementation
- Specifications MUST define requirements, not implementation details
- All code changes MUST trace back to specification updates
- Specifications serve as the single source of truth for what the system does

**Rationale**: Starting with specifications ensures clarity of purpose and prevents implementation drift. This creates a foundation for automated testing, documentation, and consistent development across teams.

### II. Test-First (Non-Negotiable)

All implementation MUST follow a strict Test-Driven Development (TDD) workflow. No implementation code shall be written before contracts are defined and tests that validate these contracts are written and approved.

**Rules**:

- Contracts (API specifications, data models) MUST be defined first
- Tests MUST be written and approved before implementation begins
- Tests MUST fail (Red phase) before implementation code is written
- Implementation code is then written to make the tests pass (Green phase)
- Refactoring occurs only after tests pass

**Rationale**: TDD ensures code quality, prevents regressions, and creates living documentation of expected behavior. This approach catches issues early and enables fearless refactoring.

### III. Simplicity and YAGNI (You Ain't Gonna Need It)

Start with the simplest possible solution that meets the requirements. Avoid speculative features, over-engineering, and premature abstractions. All complexity must be explicitly justified against a current, concrete requirement.

**Rules**:

- Implement ONLY what is currently needed
- Reject features "for future use" without current requirements
- Choose the simplest working solution
- Complexity MUST be justified in writing before implementation
- Refactor to complexity only when proven necessary

**Rationale**: Premature complexity increases maintenance burden and slows development. Starting simple allows for evolutionary design based on real needs rather than speculation.

### IV. API-First Design

All services MUST expose their functionality through well-defined, contract-first APIs. This contract is the source of truth for service interactions and is used to generate client code, tests, and documentation.

**Rules**:

- APIs MUST be designed before implementation begins
- Use contract-first specifications (OpenAPI, gRPC, etc.)
- Contracts MUST be used to generate client code and tests
- API changes MUST maintain backward compatibility
- Documentation MUST be generated from contracts

**Rationale**: API-first design ensures consistent interfaces, enables parallel development of clients and servers, and provides clear contracts for testing and documentation.

### V. Observability by Design

Systems must be designed to be observable from the outset. This includes structured logging, metrics, and distributed tracing.

**Rules**:

- All logs MUST be structured (JSON format) with correlation IDs
- Services MUST expose key metrics (latency, error rate, throughput)
- Distributed tracing MUST be implemented for request tracking
- Health check endpoints MUST be provided
- Monitoring is built-in, not added as an afterthought

**Rationale**: Observability enables effective debugging, performance monitoring, and incident response. Building it in from the start ensures systems can be properly monitored in production.

### VI. Code Quality and Maintainability

Code MUST be clean, readable, and self-documenting with consistent style enforced through automated tools.

**Rules**:

- Target recent, stable LTS versions of languages and frameworks
- Enforce consistent style using automated linters and formatters
- All I/O operations MUST be asynchronous with cancellation support
- Use dependency injection for loose coupling
- Implement centralized error handling with meaningful responses
- All public APIs, classes, and methods MUST be well-documented

**Rationale**: High-quality code reduces bugs, accelerates onboarding, and enables sustainable development velocity. Automated enforcement ensures consistency across teams.

### VII. Schema and Data Integrity

Database schema changes MUST be managed through code-first migrations with type-safe data access.

**Rules**:

- Use migrations-based schema management (EF Core, Alembic, Flyway)
- All migrations MUST be reversible
- Use ORMs or type-safe query builders instead of raw SQL
- Prevent N+1 query problems with explicit loading strategies
- Wrap multi-write operations in transactions
- Index foreign keys and frequently queried columns

**Rationale**: Proper data management prevents corruption, ensures consistency, and enables reliable deployments. Type safety reduces runtime errors.

### VIII. Code Structure and Design

Follow SOLID principles with composition over inheritance and focused, cohesive components.

**Rules**:

- Each class/method/module MUST have single responsibility
- Prefer composition over inheritance
- Use small, role-specific interfaces
- Depend on abstractions, not concrete implementations
- Services MUST be small, focused, and cohesive

**Rationale**: SOLID principles create maintainable, testable, and extensible code. Proper structure enables independent development and deployment of components.

### IX. Architecture for the Cloud

Design stateless, configurable services that run in containers with proper health checks.

**Rules**:

- Services MUST be stateless with externalized state
- Configuration MUST be externalized from code
- Secrets MUST be injected at runtime from secure stores
- All applications MUST be containerized as Docker images
- Health check endpoints MUST be exposed for orchestration

**Rationale**: Cloud-native architecture enables scalability, reliability, and automated deployment. Containerization ensures consistent environments across development and production.

### X. Proactive Security and Developer Enablement

Implement security by design with least privilege and comprehensive input validation.

**Rules**:

- Apply principle of least privilege to all identities
- Use strong, centralized authentication in production
- Implement authorization on all protected endpoints
- Encrypt data in transit and at rest
- Validate and sanitize all external input
- Scan dependencies for vulnerabilities regularly
- Use simplified auth for local development while testing authorization logic

**Rationale**: Security built-in from the start prevents vulnerabilities and compliance issues. Proper security enables safe, rapid development without compromising production safety.

## Enterprise Development Workflow

### Specification Phase

1. Use `/speckit.constitution` to establish or update governing principles
2. Use `/speckit.specify` to define WHAT you want to build and WHY
3. Use `/speckit.clarify` to resolve underspecified requirements BEFORE planning
4. Validate acceptance criteria are testable and complete
5. Define API contracts and data models

### Planning Phase

1. Use `/speckit.plan` to define HOW (tech stack, architecture, dependencies)
2. Ensure Technical Context section has NO "NEEDS CLARIFICATION" markers
3. Complete Constitution Check and document any required complexity justifications
4. Design APIs and contracts before implementation begins
5. Have agent validate the plan for completeness before proceeding

### Task Phase

1. Use `/speckit.tasks` to break plan into ordered, actionable tasks
2. Ensure tasks are grouped by user story for independent delivery
3. Verify foundational tasks are identified and will block story work appropriately
4. Confirm parallel execution markers `[P]` are used where dependencies allow
5. Include contract and API testing tasks

### Implementation Phase

1. Use `/speckit.implement` to execute tasks in dependency order
2. Write tests FIRST (contracts, then implementation tests)
3. Implement APIs and contracts before business logic
4. Commit after each task or logical group of tasks
5. Validate at checkpoints that user stories work independently
6. Stop and validate at any story boundary before proceeding to next priority

### Quality Phase

1. Verify all acceptance criteria from spec are met
2. Run all tests (unit, integration, contract) and ensure they pass
3. Check error handling for graceful degradation
4. Validate against constitution principles (especially I-X)
5. Test the quickstart.md instructions to ensure reproducibility
6. Perform security scanning and dependency checks

## Quality Gates and Compliance

### Gate 1: Specification Complete

- [ ] All user stories have clear acceptance scenarios
- [ ] Functional requirements are specific and testable
- [ ] API contracts are defined and reviewed
- [ ] Success criteria are measurable
- [ ] Edge cases are identified
- [ ] NO "NEEDS CLARIFICATION" markers remain

### Gate 2: Plan Validated

- [ ] Constitution Check passes or violations are justified
- [ ] Technical Context has no unclear items
- [ ] Project structure is documented and appropriate
- [ ] Dependencies and versions are specified
- [ ] Security and compliance requirements are addressed
- [ ] Agent has validated plan completeness

### Gate 3: Implementation Complete

- [ ] All tasks marked complete
- [ ] Tests pass (unit, integration, contract, security)
- [ ] Application runs via documented commands
- [ ] No silent error handling or unhandled exceptions
- [ ] Code follows SOLID principles and quality standards
- [ ] APIs match defined contracts

### Gate 4: Production Ready

- [ ] All acceptance criteria met
- [ ] Security scanning passes
- [ ] Performance requirements validated
- [ ] Documentation updated (README, API docs, comments)
- [ ] Health checks and monitoring configured
- [ ] Container images built and tested
- [ ] Deployment manifests validated

## Governance

### Amendment Procedure

1. Identify principle addition, modification, or removal
2. Document rationale and impact on existing projects
3. Update constitution with versioning (see below)
4. Update template files (plan, spec, tasks, commands) to align
5. Commit with message: `docs: amend constitution to vX.Y.Z (description)`

### Versioning Policy

Constitution version follows semantic versioning:

- **MAJOR**: Backward-incompatible changes (principle removal, redefinition)
- **MINOR**: New principle added or material expansion of guidance
- **PATCH**: Clarifications, wording improvements, typo fixes

### Compliance Review

- All specifications MUST reference this constitution
- All plans MUST include Constitution Check section
- Agents MUST verify compliance before implementation
- Violations MUST be documented and justified in Complexity Tracking
- Security and compliance requirements MUST be explicitly addressed

### Runtime Guidance

During active development, agents SHOULD reference:

- This constitution for non-negotiable principles
- `plan.md` for feature-specific technical decisions
- `spec.md` for requirements and acceptance criteria
- `tasks.md` for execution sequence and dependencies
- API contracts for interface definitions

**Version**: 1.0.0 | **Ratified**: 2025-10-25 | **Last Amended**: 2025-10-25
