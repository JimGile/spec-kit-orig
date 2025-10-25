# Personal Development Constitution

A set of guiding principles for my personal projects. The goal is to build things that are well-crafted, maintainable, and fun to work on, both now and in the future.

## Core Principles

### 1. The Spec is the Source of Truth

Ensure that the implementation faithfully follows the specification (spec.md). All new ideas, changes, and refactoring must start with an update to the spec, not the code. Follow that with an update to the plan and the tasks, then implement the code. This keeps the code and its intent synchronized.

### 2. Keep It Simple (YAGNI)

Build only what is needed right now. Resist the urge to add features for a hypothetical future. The simplest solution is usually the best starting point.

### 3. Test for Confidence

Write tests for critical logic. The goal isn't 100% coverage, but to create a safety net that allows for fearless refactoring and adding new features later. A good test suite proves the code works and makes future development faster and safer.

### 4. Build in Modules

Structure code in small, independent, and loosely-coupled pieces. Whether it's functions, classes, or services, each part should have a clear, single responsibility. This makes the code easier to understand, test, and reuse.

## Practical Guidelines

### Clean Code

- **Readable & Consistent:** Write code for a human to read. Use clear names and a consistent style, enforced by an automated formatter or linter.
- **Handle Errors:** Don't let errors fail silently. Handle them gracefully to create a more robust application.
- **.getignore:** Always ensure that you have a .gitignore file that is approprite for the  implementation tech stack. Keep it updated to exclude unwanted files and directories from version control.

### Smart Data Management

- **Avoid Raw SQL:** Use an ORM or a type-safe query builder to interact with the database.
- **Manage Schema Changes:** Use a migration tool to track and apply database schema changes. This prevents the database from getting out of sync with the code.

### Deployment Ready

- **Containerize It:** Package the application in a container (like Docker) from the beginning. This makes deployment predictable and consistent everywhere.
- **Externalize Configuration:** Keep configuration (like API keys, database URLs) out of the code. Use environment variables or `.env` files.

### Practical Security

- **Validate All Input:** Never trust any data coming from outside the application.
- **Keep Dependencies Updated:** Use a tool to scan for and update vulnerable third-party packages.
- **Simplify Local Auth:** For projects with authentication, use a simple, local-only mechanism during development to simulate users. This makes testing authorization rules easy without the hassle of a full login flow.
