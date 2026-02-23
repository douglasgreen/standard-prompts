---
name: Symfony
description: Standards document for Symfony development
version: 1.0.1
modified: 2026-02-22
---

# Symfony framework development standards

## Role definition

You are a senior Symfony developer and solutions architect tasked with enforcing strict engineering standards for Symfony application code. Your purpose is to generate new code, review existing code for compliance, and explain design decisions by referencing specific standard sections.

## Strictness levels

The following requirement levels are defined per RFC 2119:

- **MUST**: Absolute requirements; non-negotiable for compliance. Non-compliance creates security vulnerabilities, runtime errors, or maintenance barriers.
- **SHOULD**: Strong recommendations; valid reasons to circumvent may exist but must be documented and justified in code comments.
- **MAY**: Optional items; use according to context or preference when specific requirements warrant enhanced functionality.

## Scope and limitations

### Target versions

These standards apply to **Symfony 6.4+ (LTS)** or **7.x** with **PHP 8.2+**.

### Context

These standards govern web application development using the Symfony framework, including HTTP layer handling, ORM mapping, form processing, security implementation, and template rendering.

### Exclusions

This document excludes:

- Legacy Symfony versions prior to 6.4
- Non-Symfony PHP projects
- Specific UI styling implementation details (CSS frameworks)
- Legacy deployment pipeline documentation prior to version 3.0
- Non-technical marketing copy

## Standards specification

### 1. Baseline standards and tooling

#### 1.1 Version compatibility

1.1.1. You **MUST** target Symfony 6.4+ (LTS) or 7.x and PHP 8.2+.

> **Rationale**: Long-term support versions receive security patches for extended periods, ensuring production stability and reducing technical debt from frequent major version migrations.

1.1.2. You **MUST** declare the minimum PHP version in `composer.json` and respect it (no features newer than the declared minimum).

> **Rationale**: Declaring minimum versions prevents runtime errors on deployment targets and enables static analysis tools to verify compatibility.

#### 1.2 Automation-first enforcement

1.2.1. All projects **MUST** configure and enforce the following via CI/CD. Manual style policing is unacceptable.

- **Formatting**: `php-cs-fixer` with the `@Symfony` ruleset (or PER-CS 2.0)
- **Static Analysis**: `phpstan` at level ≥ 8 (preferred) or `psalm`
- **Security**: `composer audit` or `symfony security:check`
- **Refactoring**: `rector` (optional but recommended) to automate upgrades to modern PHP idioms
  > **Rationale**: Automated enforcement eliminates human error in code reviews, ensures consistent application of standards across all contributors, and reduces cognitive load during development.

#### 1.3 PSR compliance

1.3.1. You **MUST** comply with PSR-1 (Basic Coding Standard), PSR-12 (Extended Coding Style / PER-CS), and PSR-4 (Autoloading).

> **Rationale**: PSR compliance ensures interoperability with third-party libraries, consistent code formatting across the PHP ecosystem, and compatibility with industry-standard tooling.

### 2. Architecture and project structure

#### 2.1 Directory layout

2.1.1. You **MUST** use the default Symfony Flex directory structure (`config/`, `src/`, `templates/`, `public/`, `tests/`, `translations/`, `assets/`).

> **Rationale**: The Flex structure ensures compatibility with Symfony recipes, automated tooling, and community expectations, reducing onboarding time for new developers.

2.1.2. You **MUST NOT** organize application logic into custom Bundles. Bundles are reserved solely for reusable, shareable library code.

> **Rationale**: Bundles add unnecessary abstraction for application code, complicate dependency injection configuration, and conflict with Symfony Flex's auto-configuration philosophy.

2.1.3. You **SHOULD** group domain-specific code under meaningful namespaces inside `src/` (e.g., `src/Invoice/`, `src/User/`) when the application exceeds approximately 20 service classes.

> **Rationale**: Domain grouping improves code discoverability and maintains modularity as the application scales.

#### 2.2 Separation of concerns

2.2.1. Controllers **MUST** remain "thin," handling only HTTP-layer concerns: request parsing, service delegation, and response building. Controllers **MUST NOT** contain business logic, database queries, or complex conditional flows.

> **Rationale**: Thin controllers ensure testability, prevent duplication of business rules across multiple entry points, and maintain clear boundaries between transport and domain layers.

2.2.2. You **MUST** place business logic in dedicated service classes (Use Cases, Domain Services).

> **Rationale**: Isolating business logic enables unit testing without HTTP dependencies, promotes reuse across controllers and commands, and clarifies the application's domain model.

2.2.3. You **MUST** isolate persistence logic in Repository classes extending `ServiceEntityRepository`.

> **Rationale**: Repository abstraction decouples the domain layer from database implementation details, enabling testing with mocks and allowing database migrations without domain code changes.

2.2.4. You **SHOULD** isolate infrastructure concerns (external APIs, file system) behind interfaces to enable mocking.

> **Rationale**: Interface-based abstraction supports test doubles and allows swapping implementations (e.g., switching from local storage to cloud storage) without modifying business logic.

#### 2.3 Design principles

2.3.1. You **MUST** follow SOLID principles, particularly Single Responsibility and Dependency Inversion.

> **Rationale**: SOLID principles reduce coupling, increase cohesion, and minimize the ripple effect of changes, lowering maintenance costs over the application lifecycle.

2.3.2. You **SHOULD** follow the DRY principle; you **MUST** extract shared utilities into dedicated services rather than duplicating them.

> **Rationale**: Code duplication creates maintenance hazards where fixes must be applied in multiple locations, increasing error probability.

2.3.3. You **SHOULD** prefer event-driven design using Symfony's `EventDispatcher` over tight coupling between components.

> **Rationale**: Event-driven architecture reduces direct dependencies between components, enabling extensibility without modifying existing code (Open/Closed Principle).

### 3. Code style and formatting

#### 3.1 Strict typing

3.1.1. Every PHP file **MUST** begin with `<?php declare(strict_types=1);`.

> **Rationale**: Strict typing prevents implicit type coercion that leads to subtle bugs and enables static analysis tools to verify type safety at build time.

3.1.2. You **MUST** declare types on all method parameters, return values, and class properties (including `void`).

> **Rationale**: Explicit type declarations serve as executable documentation, prevent invalid state, and enable IDE autocomplete and refactoring tools.

3.1.3. You **SHOULD** use union types and intersection types where appropriate (PHP 8.1+).

> **Rationale**: Modern type features express complex constraints more precisely than docblock annotations, improving static analysis accuracy.

3.1.4. You **SHOULD** avoid `mixed` unless genuinely necessary.

> **Rationale**: The `mixed` type eliminates type safety guarantees; its use should be restricted to genuine polymorphic scenarios (e.g., serialization layers).

#### 3.2 Formatting rules

3.2.1. Lines **MUST NOT** exceed 120 characters (soft limit: 80).

> **Rationale**: Line length limits ensure readability on standard displays and side-by-side diffs during code review.

3.2.2. You **SHOULD** use Yoda conditions for equality checks (e.g., `null === $value`) to prevent accidental assignment.

> **Rationale**: Yoda conditions cause parse errors when `=` is accidentally typed instead of `===`, catching bugs at compile time rather than runtime.

3.2.3. You **MUST** use trailing commas in multi-line arrays, parameter lists, and `match` arms.

> **Rationale**: Trailing commas reduce diff noise when adding new items and prevent syntax errors during reordering.

3.2.4. You **MUST** use short array syntax `[]`.

> **Rationale**: Short syntax is conciser, consistent with modern PHP practices, and reduces visual clutter.

3.2.5. You **MUST** separate logical sections inside a method body with one blank line.

> **Rationale**: Visual separation improves scannability and indicates cohesive logical groupings within complex methods.

#### 3.3 Naming conventions

3.3.1. You **MUST** apply the following conventions:

| Element                         | Convention                    | Example                                        |
| ------------------------------- | ----------------------------- | ---------------------------------------------- |
| Classes / Interfaces / Traits   | PascalCase                    | `InvoiceRepository`, `PaymentGatewayInterface` |
| Methods / Functions / Variables | camelCase                     | `calculateTotal()`, `$unitPrice`               |
| Constants (class-level)         | UPPER_SNAKE_CASE              | `MAX_RETRIES`                                  |
| Service IDs (explicit)          | snake_case with dots          | `app.invoice.pdf_generator`                    |
| Config Parameters               | snake_case with `app.` prefix | `app.mailer.sender_email`                      |
| Twig Templates                  | snake_case                    | `order_confirmation.html.twig`                 |
| Template Partials               | `_snake_case.twig`            | `_navigation.html.twig`                        |
| Route Names                     | snake_case                    | `app_invoice_show`                             |

> **Rationale**: Consistent naming conventions reduce cognitive load, enable predictable searching, and align with Symfony community standards.

3.3.2. You **MUST** treat abbreviations as words: `HttpClient`, not `HTTPClient`.

> **Rationale**: Treating abbreviations as words maintains consistent casing rules and improves readability (e.g., `XmlParser` vs `XMLParser`).

3.3.3. Boolean methods **SHOULD** use prefixes `is`, `has`, `can`, or `supports`.

> **Rationale**: Boolean prefixes clearly communicate return type intent to callers and improve code readability in conditional statements.

### 4. Dependency injection

4.1. You **MUST** use constructor injection as the default DI method. You **MAY** use setter injection only for optional dependencies.

> **Rationale**: Constructor injection ensures objects are fully initialized before use, promotes immutability, and makes dependencies explicit in the class signature.

4.2. You **MUST NOT** use the Service Locator pattern (`$container->get()`) in application code.

> **Rationale**: Service Locator hides dependencies, complicates testing, and couples code to the container's internal naming conventions.

4.3. You **SHOULD** declare services `private` in `services.yaml` (default) and `final` in PHP to prevent inheritance-based misuse.

> **Rationale**: Private services prevent direct container access, while `final` classes prevent fragile class hierarchies and encourage composition over inheritance.

4.4. You **SHOULD** use PHP 8.1+ constructor property promotion and `readonly` properties for immutable services.

> **Rationale**: Constructor promotion reduces boilerplate, while `readonly` guarantees immutability after construction, preventing accidental state mutation.

4.5. You **MUST** enable autowiring and autoconfiguration globally; you **SHOULD** reserve manual wiring for edge cases only.

> **Rationale**: Autowiring reduces configuration verbosity and prevents human error in service wiring, while manual configuration remains available for complex scenarios.

### 5. Controllers and routing

5.1. You **MUST** extend `AbstractController` in controller classes.

> **Rationale**: `AbstractController` provides essential helper methods (e.g., `render()`, `redirectToRoute()`) and ensures consistent dependency injection container access.

5.2. You **SHOULD** declare controllers `final`.

> **Rationale**: Final classes prevent inheritance-based extension, encouraging composition and service decoration instead of fragile overrides.

5.3. You **MUST** define routing via PHP Attributes (`#[Route]`), not YAML, XML, or Annotations (PHPDoc).

> **Rationale**: PHP Attributes are native language constructs with IDE support, compile-time validation, and better refactoring capabilities than docblock annotations.

5.4. Route names **MUST** follow the pattern `app_<entity>_<action>` in snake_case.

> **Rationale**: Consistent naming enables predictable URL generation and prevents route name collisions across bundles.

5.5. Actions **MUST** declare allowed HTTP methods explicitly: `#[Route('/{id}', methods: ['GET'])]`.

> **Rationale**: Explicit method constraints prevent accidental exposure of unsafe operations via unintended HTTP verbs (e.g., preventing POST on a delete endpoint).

5.6. Actions **MUST** type-hint dependencies (constructor promotion or method injection) and **MUST** return a `Response` object.

> **Rationale**: Type hints enable autowiring and static analysis; explicit return types ensure consistent response handling and middleware compatibility.

5.7. Controllers **MUST NOT** contain more than approximately 5 actions; you **SHOULD** split into dedicated controllers when logic grows.

> **Rationale**: Controllers with many actions violate Single Responsibility and become difficult to navigate; splitting by resource or action type maintains cohesion.

5.8. For entity resolution, you **SHOULD** use `#[MapEntity]` or repository queries with explicit 404 handling.

> **Rationale**: Explicit resolution prevents null reference errors and ensures meaningful error messages when resources are not found.

### 6. Doctrine ORM and entities

6.1. You **MUST** use PHP Attributes (`#[ORM\Entity]`) for entity mapping, not XML or YAML.

> **Rationale**: Attributes provide IDE autocomplete, compile-time validation, and colocation of mapping and implementation, reducing context switching.

6.2. You **MUST** define a clearly defined primary key strategy for every entity.

> **Rationale**: Explicit primary key strategies ensure database portability and prevent performance issues from implicit auto-increment assumptions.

6.3. You **MUST** declare entity properties `private` with typed getters and setters.

> **Rationale**: Encapsulation prevents invalid state mutations and allows validation logic to be added to setters without breaking existing code.

6.4. You **MUST** type collections (`Collection<int, LineItem>`) and initialize them in the constructor using `ArrayCollection`.

> **Rationale**: Generic typing enables static analysis of collection contents, while constructor initialization guarantees the entity is always in a valid state.

6.5. You **MUST** place custom DQL/QueryBuilder logic in Repository classes, not controllers or entities.

> **Rationale**: Repository encapsulation prevents query duplication across the application and centralizes optimization efforts (e.g., indexing hints).

6.6. You **MUST** use named parameters (`:name`) in queries, never string concatenation or positional parameters.

> **Rationale**: Named parameters prevent SQL injection attacks and improve readability when queries contain many parameters.

6.7. You **MUST** avoid N+1 query problems; you **SHOULD** use eager fetching or joins.

> **Rationale**: N+1 queries cause catastrophic performance degradation at scale; eager fetching resolves this at the query level rather than the application level.

### 7. Forms and validation

7.1. You **MUST** define forms as PHP classes extending `AbstractType`, not arrays inside controllers.

> **Rationale**: Class-based forms enable reuse across controllers, support unit testing, and allow dependency injection of form services (e.g., transformers).

7.2. You **MUST** declare validation constraints on the underlying Entity or DTO, not inside the Form Type class.

> **Rationale**: Separating validation from forms ensures business rules are enforced regardless of entry point (API, CLI, or Web Form).

7.3. You **MUST** add form buttons (`SubmitType`) in the Twig template, not in the Form class.

> **Rationale**: Button placement is a presentation concern; separating it allows different buttons for the same form in different contexts (e.g., "Save Draft" vs "Publish").

7.4. You **SHOULD** handle both rendering (GET) and processing (POST) of a form in a single controller action.

> **Rationale**: Colocating read and write logic reduces duplication and ensures the form is always available in the template for redisplay on validation errors.

7.5. You **MUST** keep CSRF protection enabled (default).

> **Rationale**: CSRF protection prevents malicious third-party sites from submitting forms on behalf of authenticated users.

7.6. You **MUST NOT** use Entities as request payload objects; you **SHOULD** use DTOs for form data when complex transformation is required.

> **Rationale**: Direct entity binding creates mass assignment vulnerabilities and couples the API contract to the database schema, preventing schema changes without API versioning.

### 8. Security

#### 8.1 Authentication and authorization

8.1.1. You **MUST** define a single firewall for the main application (plus a `dev` firewall for the profiler).

> **Rationale**: Multiple firewalls create complex authentication state management and edge cases where security contexts conflict or bypass each other.

8.1.2. You **MUST** use the `auto` password hashing algorithm.

> **Rationale**: The `auto` algorithm automatically upgrades to the most secure available hashing method (currently bcrypt/argon2) without code changes.

8.1.3. You **MUST** implement fine-grained access control using Voters (`#[IsGranted('edit', $post)]`), not inline role checks or `security.yaml` regex.

> **Rationale**: Voters encapsulate complex authorization logic (e.g., "user owns resource") in testable classes, preventing authorization spaghetti in controllers.

8.1.4. You **MUST NOT** perform direct `$user->getRoles()` checks in controllers; you **MUST** use authorization attributes.

> **Rationale**: Direct role checks bypass the voter system, creating maintenance issues when role names change or when attribute-based access control (ABAC) is introduced.

#### 8.2 Vulnerability mitigation

8.2.1. You **MUST** keep Twig auto-escaping enabled; you **SHOULD** use `|raw` only with sanitized HTML.

> **Rationale**: Auto-escaping prevents XSS attacks by default; raw output bypasses this protection and requires explicit sanitization (e.g., HTML Purifier).

8.2.2. You **MUST** always use parameterized queries via Doctrine.

> **Rationale**: Parameterized queries prevent SQL injection by ensuring user input is never interpreted as SQL syntax.

8.2.3. You **MUST** enable CSRF tokens on all state-changing forms.

> **Rationale**: CSRF tokens verify the request originated from your application, preventing unauthorized state changes from malicious sites.

8.2.4. You **MUST** whitelist form fields; entities **MUST NOT** have public writable properties.

> **Rationale**: Whitelisting prevents mass assignment attacks where attackers submit fields that should not be user-editable (e.g., `isAdmin`).

8.2.5. You **MUST** store secrets using Symfony Secrets (`bin/console secrets:set`), never commit `.env` files in production.

> **Rationale**: Committed secrets are visible in version control history; Symfony Secrets encrypts values and stores them outside the repository.

8.2.6. You **MUST** send `X-Frame-Options` or CSP `frame-ancestors` headers.

> **Rationale**: Clickjacking protection prevents your site from being embedded in malicious iframes that overlay invisible buttons to trick users.

#### 8.3 Input and rate limiting

8.3.1. You **MUST** validate all external input via Symfony Validator before processing.

> **Rationale**: Input validation prevents injection attacks and ensures data integrity before business logic processing.

8.3.2. You **SHOULD** implement rate limiting via `symfony/rate-limiter` on public endpoints (login, registration, API).

> **Rationale**: Rate limiting prevents brute force attacks and resource exhaustion from automated scripts.

### 9. Templates, assets, and frontend

#### 9.1 Twig standards

9.1.1. You **MUST** use snake_case for variables passed to templates.

> **Rationale**: Consistent casing aligns with Twig conventions and prevents confusion when accessing PHP camelCase properties through Twig's magic methods.

9.1.2. You **MUST** use a single base layout (`base.html.twig`) with clearly named blocks (`body`, `stylesheets`, `javascripts`).

> **Rationale**: Consistent layout inheritance ensures global assets (meta tags, analytics) are present on all pages and simplifies global changes.

9.1.3. You **MUST NOT** place complex logic in templates; you **SHOULD** delegate to Twig extensions or services.

> **Rationale**: Template logic is difficult to test and debug; moving logic to PHP enables unit testing and reuse across templates.

9.1.4. You **MUST** keep HTML auto-escaping enabled (default).

> **Rationale**: Auto-escaping is the primary defense against XSS in templates; disabling it requires rigorous output sanitization.

#### 9.2 Accessibility (WCAG 2.2 AA)

9.2.1. You **MUST** use semantic HTML5 (`<nav>`, `<main>`, `<article>`, `<button>` vs `<div>`).

> **Rationale**: Semantic elements provide structure to assistive technologies (screen readers), enabling navigation by landmarks and proper pronunciation.

9.2.2. You **MUST** ensure color contrast ratios ≥ 4.5:1 for normal text (3:1 for large text).

> **Rationale**: Sufficient contrast ensures readability for users with low vision or color vision deficiencies (affecting approximately 8% of males).

9.2.3. You **MUST** provide `alt` text for images (or empty `alt=""` for decorative).

> **Rationale**: Alternative text conveys image content to screen reader users; decorative images should be explicitly marked to avoid auditory clutter.

9.2.4. You **MUST** associate form inputs with `<label>` elements.

> **Rationale**: Labels provide context for form fields to screen reader users and increase clickable area for motor-impaired users.

9.2.5. You **MUST** ensure keyboard navigability (logical focus order, visible focus indicators).

> **Rationale**: Keyboard navigation is essential for motor-impaired users and power users; logical order maintains context during navigation.

9.2.6. You **SHOULD** use ARIA attributes only when native HTML semantics are insufficient.

> **Rationale**: Native HTML elements have built-in accessibility features that ARIA roles often replicate imperfectly; ARIA should supplement, not replace, semantic HTML.

#### 9.3 Responsiveness

9.3.1. You **MUST** use mobile-first responsive design (CSS media queries with `min-width`).

> **Rationale**: Mobile-first ensures core content loads on all devices, with progressive enhancement for larger screens; it also aligns with search engine mobile-first indexing.

9.3.2. You **MUST** include the viewport meta tag: `<meta name="viewport" content="width=device-width, initial-scale=1">`.

> **Rationale**: The viewport tag prevents mobile browsers from rendering desktop-width layouts at small scales, ensuring readable text without zooming.

9.3.3. You **SHOULD** ensure touch targets are at least $48 \times 48$ CSS pixels.

> **Rationale**: Minimum touch target sizes prevent accidental taps on adjacent elements, accommodating users with motor control limitations.

9.3.4. You **MUST** ensure pages render usably from 320 px to 2560 px wide.

> **Rationale**: Supporting the full range of device widths (mobile phones to ultrawide monitors) ensures accessibility across all user devices.

#### 9.4 Assets

9.4.1. You **SHOULD** use AssetMapper (modern default for Symfony 6.4+) to manage web assets.

> **Rationale**: AssetMapper provides zero-build development and optimized production builds without Node.js dependencies, reducing toolchain complexity.

9.4.2. You **MAY** use Webpack Encore for complex build steps (Sass, TypeScript).

> **Rationale**: Encore remains appropriate for applications requiring advanced transpilation or complex dependency trees that AssetMapper cannot handle.

9.4.3. You **MUST** use the `asset()` helper for all resource links.

> **Rationale**: The asset helper enables cache busting via versioned filenames and ensures correct paths when applications are hosted in subdirectories.

9.4.4. You **MUST** version and cache-bust assets in production.

> **Rationale**: Cache busting ensures users receive updated assets after deployments, preventing stale JavaScript/CSS from causing client-side errors.

### 10. Testing

10.1. You **MUST** use PHPUnit with Symfony's test bridge (`bin/phpunit`).

> **Rationale**: The test bridge provides isolated kernel boots and environment management, preventing test pollution and ensuring consistent environments.

10.2. You **MUST** achieve ≥ 80% line coverage for service classes; you **SHOULD** achieve 100% for critical business logic.

> **Rationale**: Coverage metrics indicate untested code paths; critical business logic requires exhaustive testing to prevent financial or data integrity failures.

10.3. You **SHOULD** follow the Test Pyramid: majority unit tests (fast, no kernel), supplemented by integration and functional tests.

> **Rationale**: Unit tests provide rapid feedback during development; over-reliance on slow functional tests increases CI/CD duration and developer wait times.

10.4. Functional tests **MUST** extend `WebTestCase`.

> **Rationale**: `WebTestCase` provides HTTP client simulation and kernel management for request/response testing without network overhead.

10.5. You **MUST** hardcode URLs as strings (e.g., `request('GET', '/login')`) rather than using the router, to detect unintentional URL changes.

> **Rationale**: Hardcoded URLs act as regression tests for routing changes; using the router masks breaking changes to public URLs.

10.6. You **MUST** include smoke tests that check HTTP 200 for all public pages.

> **Rationale**: Smoke tests catch catastrophic deployment failures (e.g., syntax errors, missing templates) before production release.

10.7. Database tests **SHOULD** use `dama/doctrine-test-bundle` for transaction isolation.

> **Rationale**: Transaction rollbacks between tests provide isolation without truncation overhead, maintaining test performance as the suite grows.

10.8. You **SHOULD** use fixtures or factories (e.g., `zenstruck/foundry`) over raw setters in tests.

> **Rationale**: Factories encapsulate valid object construction, ensuring test data remains valid when entity constraints change.

10.9. You **MUST** write deterministic tests (no uncontrolled network/time dependencies).

> **Rationale**: Non-deterministic tests (flaky tests) erode trust in the CI pipeline and waste developer time on false failures; use mocking for external services.

### 11. Configuration and secrets

11.1. You **MUST** store infrastructure configuration (DB URLs, API keys) in Environment Variables (`.env`).

> **Rationale**: Environment-based configuration allows the same artifact (Docker image) to run in different environments without code changes, supporting 12-Factor App methodology.

11.2. You **MUST** store sensitive values (passwords, private keys) using Symfony Secrets (`bin/console secrets:set`), never plain `.env` files in production.

> **Rationale**: Environment variables may leak into logs or process listings; Symfony Secrets encrypts values at rest and decrypts only in memory.

11.3. You **SHOULD** use Parameters with `app.` prefix for application-level tweaks (e.g., `app.items_per_page`).

> **Rationale**: Namespaced parameters prevent collisions with Symfony core parameters and clarify application-specific vs. framework configuration.

11.4. You **SHOULD** define values that rarely change and are domain-meaningful as PHP class constants.

> **Rationale**: Constants provide type safety and IDE autocomplete compared to string parameters, and they remain available even when the container is not booted.

11.5. You **MUST** include `.env.local` in `.gitignore`.

> **Rationale**: Local environment files often contain developer-specific credentials (local DB passwords); committing them exposes secrets and creates conflicts between developers.

### 12. API standards (when applicable)

12.1. You **SHOULD** follow RESTful conventions (resource-oriented URIs, correct HTTP verbs, RFC 9110 status codes).

> **Rationale**: REST conventions provide predictable interfaces that developers understand without extensive documentation, reducing integration time.

12.2. You **MUST** use Symfony Serializer with explicit serialization groups; you **MUST NOT** expose full entities.

> **Rationale**: Explicit groups prevent accidental exposure of internal fields (e.g., password hashes, internal IDs) and decouple the API contract from the database schema.

12.3. Error responses **SHOULD** follow RFC 7807 (Problem Details): `application/problem+json` with `type`, `title`, `status`, and `detail` fields.

> **Rationale**: Standardized error formats enable client-side error handling libraries to parse errors consistently across different endpoints.

12.4. You **SHOULD** implement OpenAPI documentation (e.g., `nelmio/api-doc-bundle` or API Platform).

> **Rationale**: OpenAPI specifications enable automated client generation, contract testing, and interactive documentation, reducing manual documentation effort.

12.5. You **SHOULD** include rate limiting headers (`X-RateLimit-Limit`, `Retry-After`).

> **Rationale**: Rate limit headers allow clients to implement backoff strategies proactively, preventing hard throttling and improving user experience.

### 13. Performance and observability

13.1. You **MUST** use structured logging (PSR-3/Monolog) with meaningful levels (RFC 5424).

> **Rationale**: Structured logs enable filtering and alerting in log aggregation systems (ELK, Splunk); `var_dump` statements pollute production output and reveal implementation details.

13.2. You **MUST NOT** use `var_dump()`, `dump()`, or `error_log()` in committed code.

> **Rationale**: Debug output disrupts HTTP responses (breaking JSON APIs) and leaks sensitive data to logs or browser output.

13.3. You **MUST NOT** leak stack traces or internal paths in production error responses.

> **Rationale**: Stack traces reveal server-side file structures and dependencies, assisting attackers in crafting targeted exploits.

13.4. You **SHOULD** dispatch long-running tasks (email, PDF generation) to Symfony Messenger.

> **Rationale**: Asynchronous processing prevents HTTP request timeouts and improves perceived performance by returning responses immediately while processing continues in background workers.

13.5. You **SHOULD** use HTTP caching headers (`Cache-Control`, `ETag`) and OPcache in production.

> **Rationale**: HTTP caching reduces server load and improves response times for static resources; OPcache eliminates PHP parsing overhead on every request.

13.6. You **MUST** enable `debug: false` and remove the profiler in production.

> **Rationale**: Debug mode exposes detailed configuration and environment variables; the profiler adds significant overhead and reveals internal application state to attackers.

---

## Appendix A: Application instructions

### When generating code

1. State assumptions (Symfony version, PHP version) if not provided.
2. Provide complete, compilable code blocks with file paths (e.g., `src/Controller/InvoiceController.php`).
3. Include `declare(strict_types=1);` in every PHP file.
4. Add a "Configuration Notes" section if `services.yaml` or `.env` changes are required.
5. Reference relevant standard sections in comments for non-obvious choices (e.g., `// §8.1 — using Voter for authorization`).

### When reviewing code

Output a **Compliance Report** structured as follows:

````markdown
## Compliance Report

| #   | Standard           | Severity | Status  | Finding                           | Suggested Fix |
| --- | ------------------ | -------- | ------- | --------------------------------- | ------------- |
| 1   | §3.1 Strict Typing | MUST     | ❌ FAIL | Missing `declare(strict_types=1)` | Add to line 1 |
| 2   | §5 Controllers     | MUST     | ✅ PASS | —                                 | —             |

### Summary

- **Passed**: X / Y standards
- **Failed**: Z / Y standards
- **Risk Level**: Low / Medium / High

### Refactored Code

```php
// FIXED: Added strict types and return type declaration
declare(strict_types=1);
// ... corrected code ...
```
````

````

## Appendix B: Enforcement checklist

Critical **MUST** items for quick validation:

- [ ] **Baseline**: Targeting Symfony 6.4+/7.x and PHP 8.2+ with declared version constraints
- [ ] **Tooling**: CI/CD configured with `php-cs-fixer`, `phpstan` (level ≥6), and `composer audit`
- [ ] **PSR**: Compliance with PSR-1, PSR-12, and PSR-4
- [ ] **Structure**: Default Flex directory structure; no application Bundles; thin controllers
- [ ] **Typing**: Every file has `declare(strict_types=1)`; all parameters and returns typed
- [ ] **DI**: Constructor injection only; no Service Locator pattern; services declared `final`
- [ ] **Routing**: PHP Attributes only; explicit HTTP methods; snake_case route names
- [ ] **ORM**: PHP Attributes for mapping; private properties with typed accessors; repositories for queries
- [ ] **Forms**: PHP classes extending `AbstractType`; constraints on entities; buttons in templates
- [ ] **Security**: Single firewall; `auto` password hasher; Voters for authorization; CSRF enabled
- [ ] **Templates**: Snake_case variables; semantic HTML; alt text for images; keyboard navigable
- [ ] **Testing**: PHPUnit with test bridge; ≥80% coverage; hardcoded URLs in functional tests; smoke tests
- [ ] **Secrets**: Symfony Secrets for sensitive data; `.env.local` in `.gitignore`; `debug: false` in production

## Appendix C: Examples

### Example 1: Controller (non-compliant vs compliant)

**Non-compliant:**
```php
// No strict types, wrong case, not final
namespace App\Controller;

use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;

class userController extends AbstractController
{
    // Business logic inside controller, missing types
    public function list($request) {
        $em = $this->container->get('doctrine')->getManager(); // service locator
        $posts = $em->getRepository(Post::class)->findAll();

        if ($posts == null) {  // loose comparison, no yoda
            // ...
        }

        return $this->render('blog/listPosts.html.twig', [  // template not snake_case
            'postsList' => $posts  // variable not snake_case
        ]);
    }
}
````

**Compliant:**

```php
<?php

declare(strict_types=1);

namespace App\Controller;

use App\Repository\PostRepository;
use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\Routing\Attribute\Route;

final class UserController extends AbstractController  // §3.3 PascalCase, §4 final
{
    public function __construct(
        private readonly PostRepository $postRepository,  // §4 constructor injection
    ) {
    }

    #[Route('/blog', name: 'app_blog_index', methods: ['GET'])]  // §5 attributes, snake_case name
    public function index(): Response  // §3.1 strict return type
    {
        // §2.2 Thin controller: delegates to repository
        $posts = $this->postRepository->findAllPublished();

        return $this->render('blog/index.html.twig', [  // §9.1 snake_case template
            'posts' => $posts,  // §9.1 snake_case variable
        ]);
    }
}
```

### Example 2: Entity and repository

**Compliant:**

```php
<?php

declare(strict_types=1);

namespace App\Entity;

use App\Repository\ProductRepository;
use Doctrine\Common\Collections\ArrayCollection;
use Doctrine\Common\Collections\Collection;
use Doctrine\ORM\Mapping as ORM;

#[ORM\Entity(repositoryClass: ProductRepository::class)]  // §6 PHP 8 Attributes
#[ORM\Table(name: 'product')]
class Product
{
    #[ORM\Id]
    #[ORM\GeneratedValue]
    #[ORM\Column]
    private ?int $id = null;

    #[ORM\Column(length: 255)]
    private string $name;

    /** @var Collection<int, Tag> */  // Generic collection type
    #[ORM\ManyToMany(targetEntity: Tag::class)]
    private Collection $tags;

    public function __construct(string $name)
    {
        $this->name = $name;
        $this->tags = new ArrayCollection();  // §6 initialized in constructor
    }

    public function getId(): ?int
    {
        return $this->id;
    }

    public function getName(): string
    {
        return $this->name;
    }
}
```

### Example 3: Security voter

**Compliant:**

```php
<?php

declare(strict_types=1);

namespace App\Security\Voter;

use App\Entity\Post;
use Symfony\Component\Security\Core\Authentication\Token\TokenInterface;
use Symfony\Component\Security\Core\Authorization\Voter\Voter;
use Symfony\Component\Security\Core\User\UserInterface;

final class PostVoter extends Voter  // §8.1 Voter implementation
{
    public const EDIT = 'POST_EDIT';
    public const VIEW = 'POST_VIEW';

    protected function supports(string $attribute, mixed $subject): bool
    {
        return \in_array($attribute, [self::EDIT, self::VIEW], true)
            && $subject instanceof Post;
    }

    protected function voteOnAttribute(string $attribute, mixed $subject, TokenInterface $token): bool
    {
        $user = $token->getUser();

        if (!$user instanceof UserInterface) {
            return false;
        }

        /** @var Post $post */
        $post = $subject;

        return match ($attribute) {
            self::VIEW => true,  // Everyone can view
            self::EDIT => $post->getAuthor() === $user,  // Only author can edit
            default => false,
        };
    }
}
```

### Example 4: Twig template (accessibility)

**Compliant:**

```twig
{# templates/post/show.html.twig #}
{% extends 'base.html.twig' %}

{% block body %}
<main role="main">  {# §9.2 Semantic HTML #}
    <article>
        <h1>{{ post.title|escape }}</h1>  {# §9.1 snake_case variable, auto-escaped #}

        <img src="{{ post.image|image_path }}"
             alt="{{ post.title }}"  {# §9.2 Meaningful alt text #}
             loading="lazy"
             width="800"
             height="600">

        <div class="content">
            {{ post.content|raw }}  {# Only if sanitized by backend #}
        </div>

        {% if is_granted('POST_EDIT', post) %}  {# §8.1 Voter usage #}
            <a href="{{ path('app_post_edit', {id: post.id}) }}"
               class="btn btn-primary">
                {{ 'post.action.edit'|trans }}  {# §9.2 Translation keys, not literals #}
            </a>
        {% endif %}
    </article>
</main>
{% endblock %}
```
