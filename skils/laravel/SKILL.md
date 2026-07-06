---
name: Laravel
description: Laravel development guidelines including L11-L13 specifics, queue patterns, and Blade/Tailwind preferences
---
[LARAVEL]
- Web-based systems only (API optional)
- Controllers thin; logic in Service classes
- Queue heavy tasks (retry-safe, idempotent)
- Legacy: AdminLTE allowed
- Modern: Tailwind CSS v4 + AlpineJS for new projects
- FormRequest validation for input
- Blade components for UI
- Migration-first workflow
- Avoid N+1 queries
- Logging structured, no secrets
- Use Spatie or custom service classes for business logic
- Horizon for queue monitoring in production
- L11: minimum PHP 8.2, bug fixes until Sep 2025, security until Mar 2026
- L12: minimum PHP 8.2, released Feb 2025, bug fixes until Aug 2026, security until Feb 2027
- L13: minimum PHP 8.3, released Mar 2026, bug fixes until Q3 2027, security until Mar 2028
- L13 AI SDK: first-party Laravel\Ai\ for text, agents, images, audio, embeddings, vector stores
- L13 JSON:API Resources: first-party JSON:API spec compliance
- L13 Queue Routing: `Queue::route(Job::class, connection: 'redis', queue: 'name')` for central routing rules
- L13 PHP Attributes: #[Middleware], #[Authorize], #[Tries], #[Backoff], #[Timeout] on classes/methods
- L13 Cache::touch(): extend cache TTL without retrieve
- L13 Semantic/Vector Search: native `whereVectorSimilarTo()` with pgvector support
