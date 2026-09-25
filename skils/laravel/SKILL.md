 ---

name: Laravel
description: Laravel development playbook covering architecture, validation, authorization, database, queues, testing, security, Blade, and Laravel 13 features.
----------------------------------------------------------------------------------------------------------------------------------------------------------------

# Laravel Development Skill

Use this skill when working on a Laravel application.

This skill provides Laravel-specific knowledge and implementation guidance.

The project `AGENTS.md` remains the primary engineering contract.

---

## 1. First: Detect the Project

Before changing code, inspect:

```bash
php artisan --version
php -v
composer show laravel/framework
```

Then inspect:

```text
composer.json
.env.example
bootstrap/
config/
routes/
app/
database/
resources/
tests/
```

Do not assume the project uses the latest Laravel version.

Follow the project's actual version and existing conventions.

---

## 2. Architecture

Default architecture for web applications:

```text
Route
  ↓
Controller
  ↓
FormRequest
  ↓
Service / Domain Logic
  ↓
Model / Repository when justified
  ↓
Database
```

For responses:

```text
Controller
  ↓
Blade View / Component
```

Keep controllers thin.

Controllers should primarily handle:

* HTTP concerns
* authorization entry points
* request handling
* calling application logic
* returning responses

Do not place substantial business logic in controllers.

---

## 3. Service Layer

Use a Service class when business logic:

* spans multiple models;
* contains multiple operations;
* requires transactions;
* is reused;
* would make a controller difficult to understand;
* represents a meaningful application operation.

Example:

```php
final class DocumentService
{
    public function create(array $data): Document
    {
        return DB::transaction(function () use ($data) {
            return Document::create($data);
        });
    }
}
```

Do not create a Service class merely to wrap one trivial Eloquent call.

Avoid unnecessary:

* repositories;
* interfaces;
* factories;
* managers;
* abstract base classes;
* design patterns.

Prefer simple Laravel conventions unless complexity justifies otherwise.

---

## 4. Validation

Use FormRequest for non-trivial HTTP input validation.

Example:

```php
final class StoreDocumentRequest extends FormRequest
{
    public function authorize(): bool
    {
        return true;
    }

    public function rules(): array
    {
        return [
            'title' => ['required', 'string', 'max:255'],
            'content' => ['required', 'string'],
        ];
    }
}
```

Do not trust:

* request input;
* route parameters;
* uploaded files;
* headers;
* query parameters;
* client-provided IDs.

Validate according to the actual trust boundary.

For simple internal validation, inline validation may be appropriate.

---

## 5. Authorization

Authentication is not authorization.

Use:

* Policies;
* Gates;
* middleware;
* Laravel authorization mechanisms.

Prefer policy-based authorization for model/resource access.

Example:

```php
$this->authorize('update', $document);
```

Do not rely only on:

```php
auth()->check()
```

when the application needs object-level authorization.

Always consider whether the authenticated user is actually allowed to access the specific resource.

---

## 6. Models and Eloquent

Prefer Eloquent relationships and query scopes over duplicated query logic.

Use:

```php
$document->owner;
$document->tags;
```

rather than repeatedly constructing equivalent queries.

Prevent N+1 queries.

Prefer eager loading when relationships are known to be required:

```php
Document::with(['owner', 'tags'])->get();
```

Avoid loading unnecessary relationships.

Avoid:

```php
Model::all();
```

for potentially large datasets.

Use:

* pagination;
* chunking;
* lazy collections;
* constrained queries;

when appropriate.

Never assume a dataset is small.

---

## 7. Database Changes

Use migrations for schema changes.

Preferred workflow:

```text
Migration
    ↓
Model
    ↓
Factory / Seeder when useful
    ↓
Application code
    ↓
Tests
```

Before changing an existing column or constraint:

* inspect existing data;
* consider rollback;
* consider production impact;
* consider backward compatibility.

For related state changes, use database transactions where appropriate.

Do not perform destructive database operations casually.

---

## 8. Blade and UI

For traditional Laravel web applications, prefer:

* Blade;
* Blade components;
* Tailwind CSS when already used by the project;
* Alpine.js only when client-side interaction requires it.

Reuse existing components.

Avoid introducing Livewire, Vue, React, or another frontend framework unless the project explicitly requires it.

Do not rewrite working UI merely because another approach is available.

Keep business logic out of Blade templates.

---

## 9. Routes and Controllers

Prefer route model binding:

```php
Route::get('/documents/{document}', ...);
```

instead of manually resolving models when binding already provides the required behavior.

Use named routes where appropriate.

Keep route definitions readable.

Do not place substantial business logic inside route closures.

Prefer controllers or dedicated application services for meaningful operations.

---

## 10. Queues and Jobs

Use queues for work that does not need to block the HTTP request.

Examples:

* email;
* notifications;
* document processing;
* file conversion;
* image processing;
* external API calls;
* long-running imports;
* AI processing.

Queue jobs should be:

* retry-safe;
* idempotent where practical;
* observable;
* bounded in resource usage.

Consider:

```php
public int $tries = 3;
```

and appropriate timeout/backoff behavior.

Do not queue trivial operations without a reason.

When using queues, understand the deployment model:

```text
Laravel Application
       │
       ▼
Queue Backend
       │
       ▼
Queue Worker
```

A queued job does not execute merely because it was dispatched.

A worker or managed queue service must actually process it.

---

## 11. Testing

Every meaningful change should have appropriate tests.

Prefer tests that verify behavior rather than implementation details.

Common Laravel tests:

```text
Feature tests
Unit tests
HTTP tests
Authorization tests
Database tests
Queue tests
Notification tests
Mail tests
```

For example:

```php
$response = $this->actingAs($user)
    ->post(route('documents.store'), [
        'title' => 'Test',
        'content' => 'Example',
    ]);

$response->assertRedirect();

$this->assertDatabaseHas('documents', [
    'title' => 'Test',
]);
```

After implementation, run the smallest useful verification first, then broader tests when appropriate.

Never claim tests passed unless they were actually executed.

---

## 12. Security

For every relevant Laravel change, check:

### Input

* FormRequest or appropriate validation;
* type validation;
* size limits;
* allowed values.

### Authorization

* authentication;
* policy/gate checks;
* resource ownership;
* privilege boundaries.

### Database

* mass assignment;
* query safety;
* tenant boundaries;
* data exposure.

### Files

* MIME/type validation;
* size limits;
* safe filenames;
* storage boundaries;
* path traversal.

### Output

* Blade escaping;
* safe HTML handling;
* sensitive data exposure.

### Secrets

Never commit:

```text
.env
API keys
passwords
tokens
private keys
credentials
```

Never put secrets into logs.

---

## 13. Performance

Before optimizing, identify the actual bottleneck.

Check for:

* N+1 queries;
* unbounded queries;
* unnecessary eager loading;
* repeated database queries;
* expensive synchronous work;
* excessive filesystem operations;
* unnecessary API calls.

Use Laravel's existing mechanisms before introducing custom infrastructure.

Do not optimize speculative problems.

---

## 14. Laravel 13

Laravel 13 requires PHP 8.3.

Current official support:

```text
Laravel 13
PHP minimum: 8.3
Released: March 17, 2026
Bug fixes: Q3 2027
Security fixes: March 17, 2028
```

Laravel 12:

```text
PHP: 8.2 - 8.5
Released: February 24, 2025
Bug fixes: August 13, 2026
Security fixes: February 24, 2027
```

Always verify version-specific behavior against the project's installed framework version.

---

## 15. Laravel 13 Features

Laravel 13 introduces or expands several capabilities relevant to modern applications.

Examples include:

* Laravel AI SDK;
* JSON:API resources;
* semantic/vector search;
* expanded PHP attributes;
* queue-related attributes;
* cache improvements.

Do not use a Laravel 13 feature simply because it exists.

First determine whether it solves the actual problem and whether the project's version supports it.

---

## 16. Laravel AI

Laravel 13 provides the first-party Laravel AI SDK.

Relevant capabilities include:

* text generation;
* agents;
* tool calling;
* structured output;
* embeddings;
* vector stores;
* image generation;
* audio;
* transcription;
* queued AI work;
* MCP tools;
* provider tools;
* human approval for sensitive tool actions.

The AI SDK can be installed with:

```bash
composer require laravel/ai
```

Use it when the application genuinely requires Laravel-native AI integration.

Do not introduce an AI dependency merely because the application can use one.

For AI tools that can perform sensitive or irreversible operations, require an explicit human approval boundary where appropriate.

---

## 17. Vector Search / RAG

Laravel 13 supports vector columns and similarity queries for supported database configurations.

Example:

```php
$table->vector('embedding', dimensions: 1536);
```

Similarity queries can use:

```php
Document::query()
    ->whereVectorSimilarTo(
        'embedding',
        $queryEmbedding,
        minSimilarity: 0.4
    )
    ->limit(10)
    ->get();
```

For agent-based retrieval, Laravel AI also provides similarity-search tooling.

When implementing RAG, separate:

```text
Ingestion
    ↓
Chunking
    ↓
Embedding
    ↓
Storage
    ↓
Retrieval
    ↓
Context
    ↓
Generation
```

Do not treat vector search itself as a complete RAG architecture.

Consider:

* chunk size;
* metadata;
* access control;
* tenant boundaries;
* embedding model;
* similarity threshold;
* reranking;
* stale documents;
* deletion/update synchronization.

---

## 18. AI-Generated Code

When an AI agent writes Laravel code, apply additional scrutiny.

Check:

```text
Architecture
Validation
Authorization
Database queries
Security
Performance
Tests
Unnecessary abstraction
Unrelated changes
```

Do not accept code simply because:

```text
it looks clean
it compiles
tests pass
the AI explains it confidently
```

The generated implementation must still fit the application's architecture and requirements.

---

## 19. Documentation and Decisions

When a Laravel-specific architectural decision is important, document it.

Examples:

```text
Why a Service exists
Why a queue is required
Why a particular storage disk is used
Why a package was introduced
Why Livewire/React/Vue was not introduced
Why a specific database strategy was selected
```

Prefer documenting decisions rather than documenting obvious implementation details.

---

## 20. Verification Checklist

Before declaring a Laravel task complete:

```text
[ ] Correct Laravel version identified
[ ] Existing implementation inspected
[ ] Relevant skill/context loaded
[ ] Scope defined
[ ] Validation handled
[ ] Authorization handled
[ ] Database impact considered
[ ] N+1 checked
[ ] Security implications checked
[ ] Relevant tests written/updated
[ ] Relevant tests executed
[ ] Formatting/static checks executed when applicable
[ ] Diff reviewed
[ ] No unrelated changes
[ ] Documentation/state updated when necessary
```

The final question is:

> Does this code work, and does it belong in this Laravel application?
