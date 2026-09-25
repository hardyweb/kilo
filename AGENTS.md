# AGENTS.md

## Purpose

This file defines the engineering contract for AI coding agents working in this workspace.

Follow these rules unless the user explicitly overrides them.

---

## 1. Core Principles

* Inspect existing code before making changes.
* Follow the project's existing architecture and conventions.
* Prefer the smallest change that correctly solves the task.
* Do not introduce abstractions without a clear need.
* Do not perform unrelated refactoring.
* Never log secrets, credentials, tokens, or sensitive data.
* Validate all external/user input.
* Apply least privilege.
* Prefer secure defaults.
* Production-ready changes require appropriate error handling, logging, idempotency, and rollback consideration.
* Every stateful or destructive change must have a backup or rollback strategy.
* Do not assume a change is safe because it compiles or tests pass.

---

## 2. Workspace Boundaries

* ASK before touching projects under `~/sandbox/` that were not explicitly specified.
* If the user says "buatkan", clarify whether this means creating a new project or modifying an existing one when the target is unclear.
* Use `/tmp/kilo/` for new proof-of-concept work unless another location is specified.
* Never modify `.env`, credentials, databases, production files, or infrastructure configuration without confirmation when the impact is unclear.
* Never delete or overwrite existing data without explicit confirmation.
* If scope or target is unclear, stop and ask.

---

## 3. Context First

Before implementation:

1. Read this `AGENTS.md`.
2. Identify the relevant skills.
3. Load only skills relevant to the current task.
4. Inspect the existing implementation.
5. Identify related tests, configuration, routes, models, services, and documentation.
6. Check the current project state and existing decisions when available.

Do not assume the project follows a generic Laravel, Go, or framework tutorial.

The existing project is the source of truth unless the user explicitly requests a redesign.

---

## 4. Inspect Before You Change

Before editing:

* Find the existing implementation of the requested feature.
* Trace the relevant execution path.
* Check related models, migrations, services, controllers, requests, policies, routes, views, jobs, commands, and tests as applicable.
* Look for existing helpers, components, traits, services, or patterns that should be reused.
* Check whether the requested behavior already exists partially.
* Identify side effects and data/state changes.

Do not create a new implementation before checking whether an existing one can be extended.

---

## 5. Plan Before Implementation

For non-trivial work, establish a short implementation plan.

The plan should identify:

* What will change.
* What files are expected to change.
* What new files are required.
* Required database or infrastructure changes.
* Required tests.
* Security considerations.
* Potential backward-compatibility concerns.

Keep the plan proportional to the task.

Do not turn a small change into a large redesign.

---

## 6. Define Scope

Every task should have a clear scope.

### In scope

List the files, modules, features, or behavior directly related to the task.

### Out of scope

Do not modify:

* unrelated modules;
* unrelated formatting;
* unrelated dependencies;
* unrelated architecture;
* unrelated technical debt;
* working code merely because a different style is preferred.

If implementation reveals a necessary change outside the original scope, explain it before expanding the work.

---

## 7. Implementation Rules

### Laravel

Prefer:

* Thin controllers.
* Service classes for substantial business logic.
* FormRequest for request validation.
* Policies for authorization.
* Route model binding where appropriate.
* Blade + Tailwind for the existing web stack unless the project specifies otherwise.
* Existing Laravel conventions before custom abstractions.
* Database transactions for related state changes where appropriate.
* Queues for work that should not block the request lifecycle.

Avoid:

* Fat controllers.
* Business logic hidden inside views.
* Duplicated validation rules.
* Unnecessary repositories.
* Unnecessary interfaces.
* Unnecessary design patterns.
* N+1 queries.
* Unbounded queries on user-controlled input.
* Mixing unrelated responsibilities into a single class.

### Go

Prefer:

* Small, stateless services where appropriate.
* Explicit configuration.
* Standard library solutions where practical.
* Structured logging such as `slog`.
* Minimal dependencies.
* Explicit error handling.
* Clear package boundaries.

Avoid unnecessary abstraction and dependency growth.

---

## 8. Security

For every relevant change, consider:

* Authentication.
* Authorization.
* Input validation.
* Output escaping.
* SQL/query safety.
* Mass assignment.
* CSRF.
* Rate limiting.
* File upload security.
* Path traversal.
* SSRF.
* Command execution.
* Secret exposure.
* Sensitive logging.
* Privilege boundaries.

Do not add security controls merely as decoration.

They must correspond to an actual threat or trust boundary.

---

## 9. Verification

Never claim a change works without appropriate verification.

Use the project's available tools and tests.

Examples:

```bash
php artisan test
php artisan pint --test
php artisan route:list
```

or the appropriate Go/test/build commands.

Verification should cover:

* Syntax/build validity.
* Relevant automated tests.
* Changed behavior.
* Error paths where practical.
* Security-sensitive behavior.
* Database changes where applicable.

If a test or verification command cannot be run, state that clearly.

Do not describe unexecuted tests as passing.

---

## 10. Review After Implementation

After implementation, review the diff as if reviewing another developer's pull request.

Check:

* Does the implementation solve the requested problem?
* Does it follow the existing architecture?
* Is the change smaller than necessary or larger than necessary?
* Is there duplicated logic?
* Is there unnecessary abstraction?
* Are there security issues?
* Are there performance problems?
* Are there N+1 queries?
* Are validation and authorization handled correctly?
* Are error cases considered?
* Are tests meaningful?
* Did unrelated files change?
* Is debug code or temporary code left behind?
* Are documentation changes required?

The goal is not merely:

> "Does it work?"

Also ask:

> "Does it belong here?"

---

## 11. Project State

When the project maintains project-state documentation, update it after meaningful work.

Record only information that will help future work, such as:

* Completed work.
* Important architectural decisions.
* Known constraints.
* Pending work.
* Important discoveries.
* Migration or deployment considerations.

Do not duplicate the entire implementation in project-state documentation.

The purpose is continuity between coding sessions.

---

## 12. Documentation

* Keep project documentation in Markdown unless another format is required.
* Prefer concise documentation close to the code it describes.
* Use Mermaid for architecture or workflow diagrams where useful.
* Document important decisions and operational procedures.
* Do not generate documentation that merely repeats obvious code.

---

## 13. Environment Preferences

* Self-hosted infrastructure: Incus-first where applicable.
* Prefer Linux-native and open-source tooling where practical.
* Show CLI commands for operational tasks when useful.
* Common operational tools include:

  * `incus`
  * `tailscale`
  * `systemctl`
  * relevant project CLI commands
* Prefer plaintext/configuration formats that work well with Neovim.
* Malay/English mixed communication is acceptable.

---

## 14. Skills

Skills are stored in:

```text
~/.config/kilo/skills/
```

Load skills only when relevant to the current task.

A skill provides detailed knowledge or procedures.

This file provides the engineering contract.

Do not move large technical procedures into this file when they belong in a skill.

---

## 15. Communication

* Be concise by default.
* Show commands and code when they are the most useful form of communication.
* State assumptions when they materially affect implementation.
* Ask before making high-impact or irreversible changes.
* Report verification results honestly.
* If something was not tested, say so.
* Do not hide uncertainty behind confident language.

---

## 16. Default Workflow

For meaningful coding tasks, follow:

```text
LOAD CONTEXT
     ↓
INSPECT
     ↓
PLAN
     ↓
DEFINE SCOPE
     ↓
IMPLEMENT
     ↓
VERIFY
     ↓
REVIEW
     ↓
UPDATE PROJECT STATE
```

For trivial changes, use the shortest safe version of this workflow.

The workflow should improve engineering quality, not create unnecessary process.
