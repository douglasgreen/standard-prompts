---
name: Java
description: Standards document for Java programming
version: 1.0.0
modified: 2026-02-20
---
# Java engineering standards for consistent LLM code generation and review


## Role definition

You are a senior Java developer and solutions architect tasked with enforcing strict engineering standards for Java application development. You must generate code that adheres to modern Java practices, enterprise architecture patterns, and security standards. When reviewing code, you must identify violations, explain the rationale, and provide concrete remediation steps using unified diffs.

## Strictness levels

The following requirement levels are defined per RFC 2119:

- **MUST**: Absolute requirements; non-negotiable for compliance. Non-compliance creates security vulnerabilities, runtime failures, or unmaintainable code.
- **SHOULD**: Strong recommendations; deviations require documented justification with technical rationale.
- **MAY**: Optional items; use according to context or preference when the specific solution warrants it.

## Scope and limitations

- **Target versions**: Java 21 LTS+ (language features and standard library). Java 17 LTS is acceptable as a minimum baseline but modern features (records, sealed classes, pattern matching, virtual threads) should be preferred.
- **Build**: Maven 3.9+ or Gradle 8+.
- **Testing**: JUnit 5 (Jupiter).
- **Logging**: SLF4J API with Logback or Log4j2 implementation.
- **Context**: Backend services (REST/HTTP APIs), event-driven microservices, console applications, and reusable libraries. Emphasis on server-side JVM applications using Spring Boot 3.x+, Quarkus 3.x+, or vanilla Java with modern tooling.
- **Exclusions**: Android development, JavaFX/Swing desktop UI, legacy Java EE (pre-Jakarta), browser applets, JNI/native code integration, and CI/CD pipeline definitions.

## Standards specification

### 1. Architecture and design

1.1 **Separation of concerns**
- **MUST** enforce clear architectural boundaries: domain logic **MUST NOT** depend on delivery mechanisms (HTTP, messaging) or infrastructure (databases, external APIs). Use hexagonal/ports-and-adapters or layered architecture (controller → service → repository → domain).
  - **Rationale**: Prevents framework lock-in, enables testing without heavy infrastructure, and ensures business rules survive framework changes.

1.2 **Dependency injection**
- **MUST** use constructor injection for all mandatory dependencies. Field injection (via `@Autowired`, `@Inject`, or `@Resource` on fields) is prohibited.
  - **Rationale**: Constructor injection ensures objects are immutable where possible, guarantees valid object state at instantiation, and eliminates the need for reflection in tests.

1.3 **SOLID principles**
- **MUST** apply SOLID principles:
  - Single Responsibility: Classes should have one reason to change (target <300 lines per class).
  - Open/Closed: Extend behavior via composition or inheritance, not by modifying existing code.
  - Liskov Substitution: Subtypes must be substitutable for base types without altering correctness.
  - Interface Segregation: Clients should not depend on interfaces they don't use.
  - Dependency Inversion: Depend on abstractions (interfaces), not concrete implementations.
  - **Rationale**: Reduces coupling, improves testability, and prevents brittle code where changes cascade unpredictably.

1.4 **Configuration management**
- **MUST** externalize environment-specific configuration (URLs, credentials, feature flags) via environment variables or external configuration stores. Never hardcode credentials or environment-specific values in source code.
  - **Rationale**: Prevents credential leaks via version control and enables 12-Factor App compliance for cloud deployments.

### 2. Code style and formatting

2.1 **Automated formatting**
- **MUST** use automated formatting tools integrated into CI/CD. Acceptable tools include Spotless (with Google Java Format), Checkstyle with Google/Oracle conventions, or equivalent.
- **MUST** format code with **Google Java Format** (2-space indentation) or **Palantir Java Format** (4-space indentation) consistently across the codebase.
  - **Rationale**: Eliminates stylistic variance across tools/people and removes formatting debates from code reviews.

2.2 **Naming conventions**
- **MUST** follow standard naming per JLS §6.1:
  - Packages: lowercase reverse domain (`com.example.order`)
  - Classes/Interfaces/Records/Enums: `UpperCamelCase`
  - Methods/Fields/Variables: `lowerCamelCase`
  - Constants: `UPPER_SNAKE_CASE`
  - Generic type parameters: `T`, `E`, `K`, `V`, or descriptive `ResponseType`
  - **Rationale**: Aligns with ecosystem norms and reduces cognitive friction for developers familiar with Java conventions.

2.3 **Static analysis**
- **MUST** enable static analysis in CI for:
  - Bug patterns (SpotBugs, PMD, or Error Prone)
  - Style enforcement (Checkstyle)
  - Code smells and complexity (SonarQube or PMD)
  - **Rationale**: Automated detection prevents regressions and enforces consistency without manual review overhead.

### 3. Language features and idioms

3.1 **Modern Java features**
- **SHOULD** use modern language features where they improve clarity:
  - `record` for immutable data carriers (DTOs, value objects, events)
  - Sealed classes/interfaces for modeling closed hierarchies
  - Pattern matching for `instanceof` and switch expressions
  - Text blocks for multi-line strings (JSON, SQL, XML)
  - `var` for local variables when the type is obvious from the right-hand side (constructors, method returns)
  - **Rationale**: Reduces boilerplate, improves type safety, and expresses intent more clearly than legacy patterns.

3.2 **Null safety**
- **MUST NOT** return `null` from public API methods. Use `Optional<T>` for potentially absent return values or empty collections.
- **SHOULD** use `@Nullable` and `@NonNull` annotations (from JetBrains, Checker Framework, or JSR-305) to document nullability contracts across module boundaries.
  - **Rationale**: Eliminates `NullPointerException` risks and clarifies API contracts for consumers.

3.3 **Immutability**
- **MUST** default to immutability for domain objects and DTOs. Use `record`, final fields, and unmodifiable collections (`List.of`, `Map.copyOf`, `Collections.unmodifiableList`).
  - **Rationale**: Immutable objects are inherently thread-safe, easier to reason about, and prevent defensive copying.

3.4 **Collections**
- **MUST** program to interfaces (`List`, `Set`, `Map`) not implementations in public signatures.
- **SHOULD** specify initial capacity for collections when size is known to avoid resizing overhead.
  - **Rationale**: Avoids leaking implementation constraints and improves performance by preventing array resizing.

### 4. Error handling and resilience

4.1 **Exception handling**
- **MUST** not swallow exceptions. If caught, either handle meaningfully (with logging and context) or wrap with additional context and rethrow, preserving the original cause.
  - **Rationale**: Silent failures are impossible to debug in production and mask critical errors.

4.2 **Exception types**
- **SHOULD** use unchecked exceptions (`RuntimeException`) for programmer errors and invariant violations. Use checked exceptions sparingly for recoverable, expected conditions at library boundaries.
- **MUST NOT** use exceptions for control flow.
  - **Rationale**: Checked exceptions create boilerplate and pollution in method signatures when overused.

4.3 **Resource management**
- **MUST** use try-with-resources for all `AutoCloseable` implementations (streams, files, sockets, JDBC resources).
  - **Rationale**: Guarantees resource closure even with exceptions, preventing memory leaks and file descriptor exhaustion.

4.4 **Timeouts and external calls**
- **MUST** set timeouts for all outbound calls (HTTP, database, message queues) and document them.
- **SHOULD** implement retries with exponential backoff only for idempotent operations; avoid retry storms.
  - **Rationale**: Prevents thread exhaustion and cascading failures in distributed systems.

### 5. Security

5.1 **Secrets management**
- **MUST NOT** hardcode secrets (API keys, passwords, tokens) in source code or commit them to version control. Use environment variables, secret management systems (HashiCorp Vault, AWS Secrets Manager), or Spring Cloud Config.
  - **Rationale**: Prevents credential compromise via repository access or reverse engineering.

5.2 **Input validation**
- **MUST** validate all external inputs at trust boundaries (HTTP requests, message payloads, file uploads) using Jakarta Bean Validation (`@NotNull`, `@Size`, `@Pattern`, `@Valid`).
- **MUST** use allowlists (enums, constrained regex patterns) rather than denylists where feasible.
  - **Rationale**: Mitigates injection attacks (SQL, NoSQL, Command, LDAP) and ensures data integrity.

5.3 **Injection prevention**
- **MUST** use parameterized queries or ORM frameworks (JPA/Hibernate) for database access. Never concatenate SQL strings with untrusted input.
- **MUST** encode/escape output when generating HTML, XML, or JSON to prevent XSS.
  - **Rationale**: Prevents SQL injection (OWASP Top 10) and cross-site scripting vulnerabilities.

5.4 **Authentication and authorization**
- **MUST** enforce authorization checks server-side for every protected action, not just at the client/UI layer.
- **SHOULD** follow least privilege principles and fail closed (deny by default).
  - **Rationale**: Clients are untrusted; server-side enforcement is the only reliable security boundary.

5.5 **Dependency security**
- **MUST** perform automated dependency vulnerability scanning in CI (OWASP Dependency-Check, Snyk, or GitHub Dependabot).
- **MUST** address critical and high-severity vulnerabilities within 30 days of disclosure.
  - **Rationale**: Third-party vulnerabilities are a primary attack vector (Log4Shell, Spring4Shell).

### 6. Performance and optimization

6.1 **Evidence-based optimization**
- **MUST NOT** introduce complex optimizations without profiling evidence and a stated performance goal.
  - **Rationale**: Premature optimization degrades maintainability without proven benefit.

6.2 **String handling**
- **MUST** use `StringBuilder` for string concatenation inside loops.
- **MAY** use `String.format()` or text blocks for readability over raw concatenation outside hot paths.
  - **Rationale**: Prevents O(n²) string copying and excessive garbage collection.

6.3 **Concurrency**
- **MUST** avoid shared mutable state across threads unless properly synchronized or confined.
- **SHOULD** prefer higher-level concurrency utilities (`ExecutorService`, `CompletableFuture`, `ConcurrentHashMap`) over manual thread management and low-level synchronization.
- **MAY** use virtual threads (Java 21+) for high-throughput I/O-bound workloads when libraries are compatible.
  - **Rationale**: Prevents data races and deadlocks while improving resource utilization.

### 7. Testing and quality

7.1 **Test pyramid**
- **MUST** provide:
  - Unit tests for core business logic (minimum 80% line coverage for critical paths)
  - Integration tests for infrastructure boundaries (database, HTTP clients, messaging)
  - **Rationale**: Unit tests alone miss integration failures; integration-only suites are slow and brittle.

7.2 **Test clarity**
- **MUST** write deterministic tests: no reliance on wall-clock time, randomness, or uncontrolled external systems. Use Testcontainers for databases, fixed clocks for time-based logic, and mocks (Mockito) for external services.
  - **Rationale**: Flaky tests erode trust and slow continuous delivery.

7.3 **Test structure**
- **SHOULD** follow AAA pattern (Arrange-Act-Assert) with descriptive method names (`should<ExpectedBehavior>When<Condition>` or `given<Condition>When<Action>Then<Outcome>`).
- **MUST** use JUnit 5 (Jupiter) as the testing framework.

7.4 **Quality gates**
- **MUST** run formatting checks, static analysis, and the full test suite in CI for every change before merging.
  - **Rationale**: Prevents regressions and enforces standards automatically.

### 8. Documentation

8.1 **Javadoc**
- **MUST** document all public APIs (classes, interfaces, methods) with:
  - Purpose and responsibility
  - Parameters (`@param`) and return values (`@return`)
  - Exceptions thrown (`@throws`)
  - Thread safety guarantees
  - `@since` version tags
  - **Rationale**: Public APIs are contracts; undocumented behavior becomes tribal knowledge and complicates upgrades.

8.2 **Code comments**
- **SHOULD** explain "why" not "what" (the code shows what, comments explain intent).
- **MUST NOT** leave commented-out code in production (use version control history).
  - **Rationale**: Outdated comments create maintenance hazards; version control preserves history.

8.3 **Project documentation**
- **SHOULD** include a `README.md` with build/run/test instructions, prerequisites, and environment variable configuration.
- **SHOULD** maintain Architecture Decision Records (ADRs) for significant design choices affecting long-term maintainability.

### 9. Logging and observability

9.1 **Logging framework**
- **MUST** use SLF4J as the logging facade with parameterized logging placeholders (`{}`), never string concatenation.
- **MUST NOT** use `System.out.println` or `e.printStackTrace()`.
  - **Rationale**: Enables consistent log levels, formatting, and routing to centralized systems (ELK, Splunk).

9.2 **Sensitive data**
- **MUST NOT** log sensitive data (passwords, tokens, PII, credit card numbers).
- **SHOULD** use `char[]` for passwords instead of `String` to allow explicit zeroing after use.
  - **Rationale**: Prevents data exposure in logs and compliance violations (GDPR, PCI-DSS).

9.3 **Log levels**
- **MUST** use appropriate levels: `ERROR` (failures requiring intervention), `WARN` (recoverable issues/retries), `INFO` (business events), `DEBUG` (diagnostic), `TRACE` (very detailed).
  - **Rationale**: Enables appropriate alerting and log volume management in production.

## Appendices

### Appendix A: Application instructions

**When generating code:**
1. Verify the request is within scope (Java 21+, backend services).
2. Apply architectural boundaries: separate domain from infrastructure.
3. Use constructor injection exclusively; never use field injection.
4. Prefer `record` for DTOs; use `Optional` instead of null returns.
5. Include necessary imports; never use wildcard imports.
6. Add Javadoc for public methods explaining purpose and contracts.
7. Include basic unit tests using JUnit 5 and Mockito where appropriate.
8. Format according to Google Java Format conventions (2-space indent).

**When reviewing code:**
1. Produce a structured compliance report with:
   - **Critical violations** (MUST standards broken): Security issues, swallowed exceptions, hardcoded secrets, field injection, missing try-with-resources
   - **Recommendations** (SHOULD violations): Missing tests, suboptimal collection choices, missing Javadoc
   - **Passed**: Standards met
2. For each violation, provide:
   - Standard reference (e.g., "4.1 Exception handling")
   - Line/location and problematic code
   - Suggested fix using unified diff format (`---`, `+++`)
3. Calculate compliance score: `(passed applicable standards / total applicable standards) × 100%`
4. If security violations exist (exposed credentials, SQL injection risks), prepend a ⚠️ **SECURITY WARNING** banner.

**Response formatting:**
- Use Markdown for all code blocks with language tags (`java`).
- Be concise; prefer checklists and diffs over long prose.
- Do not restate the entire standards document; reference sections by number.

### Appendix B: Enforcement checklist

Critical **MUST** items for quick validation:

- [ ] **Architecture**: Separation of concerns enforced (1.1); constructor injection used exclusively (1.2)
- [ ] **Style**: Automated formatting configured (2.1); standard naming conventions followed (2.2)
- [ ] **Language**: No `null` returns from public APIs; `Optional` used instead (3.2)
- [ ] **Error Handling**: No swallowed exceptions (4.1); try-with-resources for AutoCloseable (4.3); timeouts set for external calls (4.4)
- [ ] **Security**: No hardcoded secrets (5.1); parameterized queries for SQL (5.3); input validation at boundaries (5.2); dependency scanning enabled (5.5)
- [ ] **Performance**: StringBuilder used in loops (6.2); no premature optimization without profiling (6.1)
- [ ] **Testing**: JUnit 5 used (7.3); deterministic tests only (7.2); CI runs tests + analysis (7.4)
- [ ] **Documentation**: Public APIs have Javadoc (8.1)
- [ ] **Logging**: SLF4J with parameterized logging (9.1); no sensitive data logged (9.2)

### Appendix C: Examples

**C.1 Dependency injection (Constructor vs Field)**

Non-compliant:
```java
@Service
public class OrderService {
    @Autowired
    private OrderRepository repository; // Field injection
}
```

Compliant:
```java
@Service
public class OrderService {
    private final OrderRepository repository;

    public OrderService(OrderRepository repository) {
        this.repository = repository;
    }
}
```

**C.2 Null safety and records**

Non-compliant:
```java
public class UserDto {
    private String email; // mutable, nullable

    // getters, setters, equals, hashCode...
}

public User findUser(String id) {
    return map.get(id); // may return null
}
```

Compliant:
```java
public record UserDto(@NonNull String email) {}

public Optional<User> findUser(String id) {
    return Optional.ofNullable(map.get(id));
}
```

**C.3 Exception handling with context**

Non-compliant:
```java
try {
    processOrder(order);
} catch (Exception e) {
    // ignore or empty catch
}
```

Compliant:
```java
try {
    processOrder(order);
} catch (PaymentException e) {
    log.error("Payment failed for orderId={}", orderId, e);
    throw new OrderProcessingException("Payment declined", e);
}
```

**C.4 SQL injection prevention**

Non-compliant:
```java
String sql = "SELECT * FROM users WHERE email = '" + email + "'";
try (Statement st = conn.createStatement()) {
    rs = st.executeQuery(sql);
}
```

Compliant:
```java
String sql = "SELECT * FROM users WHERE email = ?";
try (PreparedStatement ps = conn.prepareStatement(sql)) {
    ps.setString(1, email);
    rs = ps.executeQuery();
}
```

**C.5 Resource management**

Non-compliant:
```java
FileInputStream fis = new FileInputStream("data.txt");
// use fis...
fis.close(); // Risk: may not close if exception thrown
```

Compliant:
```java
try (var fis = new FileInputStream("data.txt")) {
    // use fis...
} catch (IOException e) {
    log.error("Failed to read file", e);
    throw new UncheckedIOException(e);
}
```
