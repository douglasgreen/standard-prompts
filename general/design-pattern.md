---
name: Design Pattern
description: Standards document for design patterns
version: 1.0.0
modified: 2026-02-20
---
# Software design patterns engineering standards


## Role definition

You are a senior software developer and solutions architect tasked with enforcing strict engineering standards for software design patterns, clean code, scalability, cross-platform responsiveness, and accessibility. Your purpose is to generate new code that complies with this document and review existing code for compliance, flagging violations with clear explanations and suggested fixes. You must minimize variability in outputs across tools and developers by using consistent structure, naming, automation, and decision criteria.

## Strictness levels

The following requirement levels are defined per RFC 2119:

- **MUST**: Absolute requirements; non-negotiable for compliance. Non-compliance creates architectural flaws, security vulnerabilities, or maintenance barriers.
- **SHOULD**: Strong recommendations; valid reasons to circumvent may exist but must be documented and justified.
- **MAY**: Optional items; use according to context, preference, or specific project needs.
- **MUST NOT**: Absolute prohibition; represents anti-patterns or dangerous practices.

## Scope and limitations

### Target versions

- **TypeScript**: 5.4+
- **Node.js**: 20+ (LTS)
- **Python**: 3.12+
- **Java**: 21+
- **C# / .NET**: .NET 8+
- **PostgreSQL**: 15+
- **Web**: HTML Living Standard + CSS (modern evergreen browsers)
- **Accessibility**: WCAG 2.2 AA target
- **Security**: OWASP Top 10 awareness + OWASP ASVS alignment

### Context

Applies to **application and library code** including REST/JSON APIs, backend services, frontend component libraries, CLI tools, and shared domain modules. Applies to design pattern selection and implementation, code organization, error handling, performance considerations, security controls, testing, and documentation.

### Exclusions

This document excludes legacy deployment pipelines prior to version 3.0, vendor-specific infrastructure recipes, and CI/CD implementation details beyond lint/test hooks. Pure visual design (colors, spacing, branding) is out of scope except where it affects accessibility and responsiveness.

---

## 6. Standards specification

### 1. General architectural principles

#### 1.1. SOLID principles

1.1.1. **MUST** adhere to the Single Responsibility Principle: each class, module, or function must have exactly one reason to change.

> **Rationale**: Components with multiple responsibilities create coupling between unrelated concerns, making changes risky and testing difficult.

1.1.2. **MUST** adhere to the Open/Closed Principle: software entities must be open for extension but closed for modification. Extend behavior through composition or inheritance rather than modifying existing code.

> **Rationale**: Modifying existing tested code introduces regression risk and destabilizes proven implementations.

1.1.3. **MUST** adhere to the Liskov Substitution Principle: subtypes must be substitutable for their base types without altering program correctness.

> **Rationale**: Violations break polymorphism assumptions and lead to unexpected runtime behavior when substitution occurs.

1.1.4. **MUST** adhere to the Interface Segregation Principle: clients must not be forced to depend on interfaces they do not use. Split large interfaces into smaller, specific ones.

> **Rationale**: Fat interfaces create unnecessary coupling and force implementing classes to provide stub implementations.

1.1.5. **MUST** adhere to the Dependency Inversion Principle: high-level modules must not depend on low-level modules; both must depend on abstractions.

> **Rationale**: Direct dependencies on concrete implementations create rigid architectures resistant to change and testing.

#### 1.2. Core engineering principles

1.2.1. **MUST** eliminate code duplication (DRY principle). Extract repeated logic blocks (>3 lines appearing more than twice) into named functions or methods.

> **Rationale**: Duplication multiplies maintenance burden; updates require multiple changes, increasing error probability.

1.2.2. **SHOULD** favor composition over inheritance to achieve code reuse and flexibility.

> **Rationale**: Deep inheritance hierarchies create tight coupling and fragile base class problems.

1.2.3. **MUST** follow the Law of Demeter: a method $m$ of object $o$ may only invoke methods on $o$ itself, objects passed as parameters to $m$, objects created within $m$, or objects held in instance variables of $o$.

> **Rationale**: Excessive coupling to object structures makes refactoring difficult and increases the ripple effect of changes.

### 2. Dependency injection

2.1.1. **MUST** use constructor injection as the primary mechanism for required, immutable dependencies. Declare all injected dependencies as `final` (Java/C#) or `readonly` (TypeScript).

> **Rationale**: Constructor injection ensures objects are fully initialized before use and makes dependencies explicit in the type signature, improving testability.

2.1.2. **MUST** define dependencies as interfaces or abstract classes, not concrete implementations.

> **Rationale**: Decouples high-level logic from low-level details and enables substitution with test doubles.

2.1.3. **MUST NOT** use service locator patterns or static factory lookups within business logic.

> **Rationale**: Service locators hide dependencies and create implicit global state, hindering testability.

2.1.4. **SHOULD** use setter injection only for optional dependencies with sensible defaults.

2.1.5. **MUST** configure dependency injection containers in a centralized composition root (application entry point). **MUST NOT** scatter container configuration or inject the container itself into service classes.

> **Rationale**: Centralized configuration provides a clear dependency graph and prevents the service locator anti-pattern.

2.1.6. **MUST** explicitly define lifecycle scopes (Transient, Scoped, Singleton) for each registered dependency.

> **Rationale**: Prevents resource leaks (transient when singleton needed) or unexpected shared state (singleton when transient needed).

### 3. Front controller

3.1.1. **MUST** route all incoming requests through a single entry point that handles cross-cutting concerns (authentication, logging, correlation IDs, input validation) before delegation.

> **Rationale**: Prevents duplicated security logic and ensures consistent application of policies across all endpoints.

3.1.2. **MUST** keep the front controller thin; delegate to application services or command handlers rather than implementing business logic.

> **Rationale**: Preserves separation of concerns and prevents the controller from becoming a "god object."

3.1.3. **MUST** define a uniform interface for all request handlers (commands) and separate routing logic from handling logic through a dispatcher component.

> **Rationale**: Enables independent testing of routing and business logic.

3.1.4. **SHOULD** implement middleware chains for cross-cutting concerns to enable modular composition.

### 4. Observer or publish/subscribe

4.1.1. **MUST** use the observer pattern for decoupling event producers from multiple consumers; **MUST NOT** use for strict sequencing requirements.

> **Rationale**: Observers excel at fan-out but complicate ordering guarantees; sequential processing requires different patterns.

4.1.2. **MUST** provide explicit unsubscribe or disposal mechanisms to prevent memory leaks (the lapsed listener problem).

> **Rationale**: Long-lived subscriptions prevent garbage collection of observers.

4.1.3. **MUST** handle observer exceptions gracefully; a failing observer **MUST NOT** prevent notification of other observers.

> **Rationale**: Isolates failures to prevent cascade errors in event processing.

4.1.4. **SHOULD** define event schemas as strongly typed classes rather than primitive types or strings, and version them for integration events.

4.1.5. **MUST** document thread-safety guarantees: either guarantee thread-safe notification or require observers to be thread-safe.

### 5. Singleton

5.1.1. **SHOULD** avoid singletons for domain services or mutable state; prefer dependency injection with scoped lifetimes.

> **Rationale**: Singletons introduce global mutable state, complicating testing and creating hidden coupling.

5.1.2. **MUST** ensure thread-safe initialization (double-checked locking, static holders, or `Lazy<T>`) when singletons are used in multi-threaded environments.

> **Rationale**: Unsafe initialization causes race conditions and multiple instance creation.

5.1.3. **MUST** restrict singleton usage to stateless utilities, read-only configuration, or true global resources (e.g., database connection pools).

5.1.4. **MUST** make constructors private or protected and prevent cloning/serialization from creating additional instances.

> **Rationale**: Prevents instantiation bypass and ensures single instance guarantee.

5.1.5. **SHOULD** provide a reset mechanism for test environments when singletons cannot be avoided.

### 6. Factory method

6.1.1. **MUST** use factory method when construction varies by subtype and callers must not know concrete classes. The factory method **MUST** return the product interface type, not a concrete implementation.

> **Rationale**: Encapsulates creation logic and prevents conditional sprawl throughout client code.

6.1.2. **MUST** define an abstract creator class or interface with the factory method, allowing subclasses to provide concrete implementations.

6.1.3. **SHOULD** keep factories side-effect free; **MUST NOT** perform I/O during object creation unless explicitly required.

> **Rationale**: Hidden I/O is surprising and harms test determinism.

6.1.4. **MUST** validate parameters in parameterized factory methods and provide clear error messages for invalid values.

### 7. Decorator

7.1.1. **MUST** preserve the wrapped object's contract; decorators **MUST** implement the same interface as the component they decorate.

> **Rationale**: Breaking substitutability defeats the purpose of transparent behavior addition.

7.1.2. **MUST NOT** add public methods to decorator classes that do not exist in the component interface.

> **Rationale**: Violates transparency and prevents the decorator from being used interchangeably with the component.

7.1.3. **SHOULD** use decorators for cross-cutting concerns (caching, logging, retries) with explicit composition order documented.

> **Rationale**: Order changes semantics (e.g., encrypt-then-compress vs. compress-then-encrypt); documentation prevents errors.

7.1.4. **MUST** delegate all operations to the wrapped component by default, overriding only specific methods to add behavior.

### 8. Strategy

8.1.1. **MUST** use strategy to swap algorithms or policies at runtime without conditional sprawl. The context **MUST** delegate to the strategy interface.

> **Rationale**: Eliminates large conditional statements and enables algorithm variation without context modification.

8.1.2. **MUST** ensure strategies are stateless or clearly document state requirements and thread safety.

> **Rationale**: Hidden state can cause cross-request leakage in multi-threaded environments.

8.1.3. **MUST** define a common interface for all concrete strategies, ensuring they are independently testable without the context.

8.1.4. **SHOULD** support composition of strategies (e.g., chaining validators) where appropriate.

### 9. Adapter, wrapper, or translator

9.1.1. **MUST** adapt mismatched interfaces without leaking the adaptee's semantics into the domain. The adapter **MUST** implement the target interface expected by clients.

> **Rationale**: Prevents vendor-specific concepts from contaminating core logic.

9.1.2. **SHOULD** prefer object adapters (composition) over class adapters (inheritance) to avoid coupling to specific adaptee implementations.

9.1.3. **MUST** map errors and data models explicitly at the boundary, translating adaptee exceptions to domain-specific exceptions.

> **Rationale**: Avoids partial translation and inconsistent error handling across the application.

### 10. Module

10.1.1. **MUST** design modules as cohesive units with minimal public surface area. **MUST NOT** create "god modules" that aggregate unrelated functionality.

> **Rationale**: Smaller APIs reduce coupling and breakage risk; large modules become unmaintainable.

10.1.2. **MUST** use language-native module systems (ES6 `import`/`export`, Python packages with `__init__.py`, Java packages) rather than global namespace pollution.

10.1.3. **MUST** define `__all__` (Python) or equivalent explicit exports to control public API surface area.

10.1.4. **MUST NOT** create circular dependencies between modules (Module A imports B, B imports A).

> **Rationale**: Circular dependencies prevent independent testing and deployment, creating brittle architectures.

### 11. Proxy

11.1.1. **MUST** use proxies when you need to control access, add lazy loading, or provide remote indirection transparently. The proxy **MUST** provide an identical interface to the real subject.

> **Rationale**: Transparency allows proxies to substitute for real subjects without client modification.

11.1.2. **MUST** document proxy behavior explicitly (latency implications, caching policies, authorization requirements).

> **Rationale**: "Transparent" indirection still has real operational impacts that must be understood by developers.

11.1.3. **MUST** ensure thread-safe initialization for virtual proxies deferring expensive object creation.

11.1.4. **MUST** implement cache invalidation or expiration strategies for caching proxies to prevent stale data.

### 12. Command

12.1.1. **MUST** model actions as command objects when you need queueing, retries, auditing, or undo/redo functionality. Commands **MUST** encapsulate all necessary information to perform an action.

> **Rationale**: Commands make operations first-class objects, enabling queuing, logging, and transactional behavior.

12.1.2. **SHOULD** make commands immutable after construction to ensure predictable behavior on repeated execution or replay.

12.1.3. **MUST** include an `undo()` method for reversible commands, with clear documentation of invariants and side effects.

12.1.4. **MUST** decouple the invoker from the receiver; the invoker **MUST NOT** know the concrete command implementation, only the abstraction.

### 13. Chain of responsibility

13.1.1. **MUST** use for pipelines where multiple handlers may process or pass along a request (validation, middleware). Each handler **MUST** maintain a reference to the next handler in the chain.

> **Rationale**: Decouples senders from receivers and enables dynamic handler configuration.

13.1.2. **MUST** define short-circuit rules and ensure termination (either successful handling or explicit drop/logging at chain end).

> **Rationale**: Prevents unhandled requests and infinite loops.

13.1.3. **MUST NOT** distribute partial processing across multiple handlers unless explicitly designed as a processing pipeline; a handler **SHOULD** either process fully or pass.

13.1.4. **SHOULD** provide builder-style chain construction for improved readability and configuration.

### 14. Builder

14.1.1. **MUST** use builder when construction requires many optional parameters and invariants must be preserved. The `build()` method **MUST** validate all invariants and fail fast with actionable errors.

> **Rationale**: Prevents creation of invalid objects and avoids telescoping constructors.

14.1.2. **MUST** return `this` (or `self`) from builder setter methods to enable fluent method chaining.

14.1.3. **SHOULD** return immutable objects from the `build()` method, making the target class constructor private or package-private to enforce builder usage.

> **Rationale**: Immutability eliminates temporal coupling and thread-safety concerns.

14.1.4. **MAY** implement a Director class to encapsulate common construction sequences when multiple builder configurations are standard.

### 15. Prototype

15.1.1. **SHOULD** use prototype when object creation is expensive and cloning is well-defined. You **MUST** define deep vs. shallow copy semantics explicitly.

> **Rationale**: Ambiguity causes shared-mutable-state bugs when shallow copies are used inadvertently.

15.1.2. **MUST** implement deep copying for mutable reference fields within the clone method.

> **Rationale**: Prevents modifications to the clone from affecting the original prototype.

15.1.3. **SHOULD** use copy constructors (where language-supported) or serialization-based cloning for complex object graphs, with performance considerations documented.

### 16. Facade

16.1.1. **MUST** use facade to provide a simplified interface to a complex subsystem. The facade **MUST** delegate to subsystem classes rather than duplicating their logic.

> **Rationale**: Duplication creates maintenance burden and divergence risk; delegation ensures consistency.

16.1.2. **SHOULD** keep facades stateless and thin; **MUST NOT** reimplement subsystem logic.

16.1.3. **MAY** expose subsystem classes directly for advanced users who need functionality not covered by the facade's simplified interface.

### 17. Iterator

17.1.1. **SHOULD** use iterators to traverse collections without exposing internal representation. **MUST** use language-native iterator protocols (Java `Iterator`, Python `__iter__`, C# `IEnumerator`).

> **Rationale**: Native protocols enable enhanced for-loops and comprehension syntax, improving code clarity.

17.1.2. **MUST** ensure iterators maintain their own traversal state, independent of the collection state.

> **Rationale**: Supports multiple simultaneous traversals of the same collection.

17.1.3. **SHOULD** implement fail-fast behavior when the collection is modified during iteration, or document snapshot semantics clearly.

### 18. Composite

18.1.1. **MUST** use composite to treat individual objects and compositions uniformly (tree structures). Components and composites **MUST** share a common interface.

> **Rationale**: Eliminates conditional logic in clients checking "is this a leaf or composite?"

18.1.2. **MUST** handle child management operations (`add`, `remove`) gracefully in leaf nodes, either by no-op or by throwing `UnsupportedOperationException`, with the behavior documented.

18.1.3. **SHOULD** prevent cycles in the composite tree structure; document behavior if cycles are possible.

> **Rationale**: Cycles break recursion assumptions and cause infinite loops in traversal.

### 19. State

19.1.1. **MUST** use state pattern when behavior varies by internal state and conditionals become complex. State-specific behavior **MUST** be encapsulated within concrete state classes, not large switch statements in the context.

> **Rationale**: Localizes state-specific behavior and reduces cyclomatic complexity.

19.1.2. **MUST** keep state transitions explicit and validated; **MUST NOT** allow illegal transitions.

> **Rationale**: Prevents invalid object states that violate business invariants.

19.1.3. **MAY** allow states to control transitions (state knows next state) or allow context to control them, but **MUST** be consistent across the state machine.

### 20. Template method

20.1.1. **SHOULD** use template method when an algorithm skeleton is stable but steps vary. The template method itself **MUST** be marked as final/sealed to prevent overriding the algorithm structure.

> **Rationale**: Prevents subclasses from altering the workflow sequence while allowing step customization.

20.1.2. **SHOULD** provide "hook" methods (empty or default implementations) that subclasses can override optionally.

20.1.3. **MUST NOT** use template method when the variation is large or crosses concerns; prefer Strategy pattern for significant algorithmic differences.

> **Rationale**: Deep inheritance increases coupling and fragile base class problems.

### 21. Mediator

21.1.1. **SHOULD** use mediator to reduce many-to-many coupling between components (UI coordination, workflow orchestration). Colleagues **MUST NOT** communicate directly; they **MUST** communicate only through the mediator.

> **Rationale**: Centralizes interaction logic and prevents spaghetti dependencies.

21.1.2. **MUST** prevent the mediator from becoming a "god object" by keeping responsibilities narrow and focused on interaction coordination.

> **Rationale**: A bloated mediator recreates the original coupling problem in a central location.

21.1.3. **SHOULD** make the mediator interface minimal and stable; concrete mediators can vary independently.

### 22. Visitor

22.1.1. **SHOULD** use visitor when you need many operations over a stable object structure. You **MUST** implement double dispatch: `accept(visitor)` on elements and `visit(element)` on visitors.

> **Rationale**: Allows adding new operations without modifying element classes, adhering to Open/Closed Principle.

22.1.2. **MUST NOT** use visitor when the object structure changes frequently (adding new element types), as this requires modifying all visitors.

> **Rationale**: Structural churn makes visitor costly to maintain.

### 23. Interpreter

23.1.1. **SHOULD** use for small domain-specific languages (DSLs) and rule engines with simple grammars. You **MUST** define parsing and evaluation errors explicitly.

> **Rationale**: Ambiguous failure modes make DSLs unusable and hard to debug.

23.1.2. **MUST NOT** use interpreter for complex grammars; use parser generators (ANTLR, yacc) or specialized compiler tools instead.

> **Rationale**: Hand-rolled parsers for complex grammars are error-prone and difficult to maintain.

23.1.3. **MUST** use the composite pattern to represent the abstract syntax tree (AST) nodes.

### 24. Memento

24.1.1. **SHOULD** use memento for undo/restore without exposing internal representation. The memento **MUST NOT** expose the originator's internals to other objects (caretakers).

> **Rationale**: Preserves encapsulation boundaries while enabling state externalization.

24.1.2. **MUST** treat mementos as sensitive if they contain user data or secure state; implement appropriate access controls.

> **Rationale**: Snapshots may leak private information if not protected.

24.1.3. **SHOULD** make the caretaker responsible for the lifecycle (saving/restoring) of mementos, while the originator creates and uses them.

### 25. Null object

25.1.1. **SHOULD** use null object to eliminate repetitive null checks when "do nothing" is valid behavior. The null object **MUST** implement the interface with no-op behavior rather than returning `null` or throwing exceptions.

> **Rationale**: Simplifies client code by eliminating defensive null checks throughout the codebase.

25.1.2. **MUST** ensure null objects do not hide real errors (e.g., missing required dependencies); they should only represent valid "empty" states.

> **Rationale**: Silent failures are harder to diagnose than explicit null pointer exceptions.

25.1.3. **SHOULD** implement null objects as stateless singletons where possible.

### 26. Fluent interface

26.1.1. **MAY** use fluent interfaces for builders and query APIs; **MUST NOT** use for core business logic methods with side effects.

> **Rationale**: Fluency improves readability for configuration, but can obscure side effects and control flow in business logic.

26.1.2. **MUST** return `this` (or a new immutable instance for immutable builders) from fluent methods to enable chaining.

26.1.3. **SHOULD** delay complex side effects until a terminal operation (`build()`, `execute()`) is called.

> **Rationale**: Prevents surprising partial mutations when configuring objects.

### 27. Specification

27.1.1. **MUST** use specification to model business rules as composable predicates. Specifications **MUST** have an `is_satisfied_by()` method and support logical composition (AND, OR, NOT).

> **Rationale**: Encodes rules centrally, supports reuse, and enables complex query construction without duplication.

27.1.2. **SHOULD** keep specifications stateless and side-effect free.

27.1.3. **SHOULD** provide clear naming conventions that express the business rule (e.g., `CustomerSpecification.isPremium()`).

### 28. Bridge

28.1.1. **SHOULD** use bridge to decouple abstraction from implementation when both vary independently. Abstractions and implementors **MUST** be separate class hierarchies.

> **Rationale**: Prevents class explosion from combinatorial inheritance (e.g., $n$ abstractions $\times$ $m$ implementations).

28.1.2. **MUST** keep the implementor interface minimal and stable to allow independent evolution.

> **Rationale**: Stability is required for the abstraction to vary without breaking implementations.

### 29. Flyweight

29.1.1. **MAY** use flyweight to reduce memory by sharing immutable intrinsic state. Intrinsic state **MUST** be immutable; extrinsic state **MUST** be provided by callers.

> **Rationale**: Mutability breaks sharing safety and causes cross-object corruption.

29.1.2. **MUST** use a factory for flyweight management to ensure sharing and prevent duplicate creation.

29.1.3. **SHOULD** document the trade-off between memory savings and increased CPU overhead for extrinsic state management.

### 30. Object pool

30.1.1. **SHOULD** avoid object pools in garbage-collected languages unless profiling proves necessity (e.g., for database connections, threads, or expensive graphics objects).

> **Rationale**: Pools often harm performance and add contention in modern GC environments.

30.1.2. **MUST** bound pool size and ensure proper reset/cleanup (sanitization) on checkout and return.

> **Rationale**: Prevents resource exhaustion and data leakage between contexts.

30.1.3. **MUST** handle acquisition failures gracefully (blocking with timeout or rejection) when the pool is exhausted.

### 31. Lazy initialization

31.1.1. **SHOULD** use lazy initialization for expensive resources that may not be needed during execution. The accessor **MUST** check initialization state before returning.

> **Rationale**: Reduces startup time and resource usage for optional features.

31.1.2. **MUST** make lazy initialization thread-safe where concurrency exists (using locks, `Lazy<T>`, or atomic references).

> **Rationale**: Races can create duplicate initialization or corrupt state.

31.1.3. **MUST NOT** use lazy initialization for safety-critical or always-required dependencies; prefer eager initialization for those.

### 32. Abstract factory

32.1.1. **MUST** use abstract factory when you need families of related objects without specifying concrete classes. The factory **MUST** ensure all created objects belong to the same family/theme.

> **Rationale**: Ensures consistency across related objects and supports swapping entire product families.

32.1.2. **MUST** define abstract factories and products as interfaces or abstract classes.

32.1.3. **SHOULD** keep product interfaces cohesive; **MUST NOT** create bloated factories that create unrelated objects.

> **Rationale**: Large abstract factories become hard to evolve and violate Interface Segregation Principle.

---

## Appendices

### Appendix A: Application instructions

**A.1 When generating new code**

1. Identify requirements and constraints (functional, security, performance, scale).
2. Select appropriate design patterns from Section 6 based on the problem domain; justify pattern selection.
3. Apply **MUST** requirements strictly; document deviations from **SHOULD** requirements with rationale.
4. Output code by file/module with:
   - Pattern implementation following standards
   - Error handling at trust boundaries
   - Unit tests for critical logic
   - Documentation comments explaining *why* patterns are applied
5. Validate against Appendix B checklist before delivery.

**A.2 When reviewing existing code**

1. Identify patterns used (or attempted) in the codebase.
2. Check compliance against Section 6 standards:
   - Flag **MUST** violations as **Critical Errors** requiring immediate fix
   - Flag **SHOULD** violations as **Warnings** requiring justification or fix
   - Note **MAY** opportunities as **Suggestions**
3. Provide remediation:
   - Reference specific standard numbers (e.g., "Violation of 6.5.1.2")
   - Show diff-style before/after code blocks
   - Explain risks (security, maintenance, performance) of non-compliance
4. Output format:
   ```markdown
   ## Compliance summary
   - Status: [Compliant/Non-Compliant]
   - Critical issues: [Count]
   - Warnings: [Count]

   ## Critical issues
   ### 1. [Pattern] - [Standard Reference]
   **Location**: `file.ts:line`
   **Issue**: [Description]
   **Fix**:
   ```diff
   - // Non-compliant code
   + // Compliant code
   ```

   ## Warnings
   [Same structure]

   ## Next steps
   - [Action items]
   ```

### Appendix B: Enforcement checklist

Critical **MUST** items for quick validation:

- [ ] **General**: SOLID principles followed; no circular dependencies
- [ ] **DI**: Constructor injection used; no service locators; dependencies are abstractions
- [ ] **Front Controller**: Centralized entry; thin controllers; cross-cutting concerns handled before delegation
- [ ] **Observer**: Unsubscribe mechanisms provided; observer failures isolated
- [ ] **Singleton**: Thread-safe; restricted to stateless/read-only; avoided for domain logic
- [ ] **Factory Method**: Returns abstractions not concretions; no I/O in creation
- [ ] **Decorator**: Preserves interface; no extra public methods; transparent wrapping
- [ ] **Strategy**: Runtime switchable; stateless or documented thread-safety
- [ ] **Adapter**: Implements target interface; maps errors explicitly at boundary
- [ ] **Module**: Minimal public API; no circular dependencies; native module systems used
- [ ] **Proxy**: Identical interface to subject; thread-safe initialization; documented behavior
- [ ] **Command**: Immutable; encapsulates full request; undo provided if needed
- [ ] **Chain of Responsibility**: Termination defined; no partial processing distributed accidentally
- [ ] **Builder**: Validates invariants at build; returns immutable objects; fluent chaining
- [ ] **Prototype**: Deep copy semantics defined; mutable fields cloned
- [ ] **Facade**: Delegates to subsystem; no logic duplication
- [ ] **Iterator**: Native protocols used; independent traversal state
- [ ] **Composite**: Uniform interface; cycle prevention documented
- [ ] **State**: Behavior in state classes; transitions validated; no illegal states
- [ ] **Template Method**: Skeleton is final; hooks provided for variation
- [ ] **Mediator**: No direct colleague communication; not a god object
- [ ] **Visitor**: Double dispatch implemented; not used when structure changes frequently
- [ ] **Interpreter**: Error handling defined; not used for complex grammars
- [ ] **Memento**: Encapsulation preserved; sensitive data protected
- [ ] **Null Object**: Implements interface with no-ops; doesn't hide errors
- [ ] **Fluent Interface**: Returns self; delays side effects to terminal operations
- [ ] **Specification**: Composable (AND/OR/NOT); stateless predicates
- [ ] **Bridge**: Separate hierarchies; minimal stable implementor interface
- [ ] **Flyweight**: Intrinsic state immutable; extrinsic provided externally
- [ ] **Object Pool**: Bounded size; reset on release; handles exhaustion
- [ ] **Lazy Initialization**: Thread-safe check; not used for safety-critical deps
- [ ] **Abstract Factory**: Consistent families; cohesive product interfaces

### Appendix C: Examples

<details>
<summary>C.1 Dependency injection - Non-compliant vs Compliant</summary>

**Non-compliant** (Service locator anti-pattern):
```typescript
// Violates 6.2.1.3: Service locator hides dependencies
class OrderService {
  processOrder(order: Order) {
    const db = ServiceLocator.getDatabase(); // Hidden dependency
    const email = ServiceLocator.getEmailService();
    db.save(order);
    email.sendConfirmation(order);
  }
}
```

**Compliant** (Constructor injection):
```typescript
// Compliant with 6.2.1.1, 6.2.1.2
interface Database { save(order: Order): void; }
interface EmailService { sendConfirmation(order: Order): void; }

class OrderService {
  constructor(
    private readonly db: Database,
    private readonly email: EmailService
  ) {}

  processOrder(order: Order): void {
    this.db.save(order);
    this.email.sendConfirmation(order);
  }
}
```
</details>

<details>
<summary>C.2 Decorator - Non-compliant vs Compliant</summary>

**Non-compliant** (Breaks interface contract):
```typescript
// Violates 6.7.1.2: Adds public method not in interface
class LoggingDataSource extends DataSource {
  constructor(private wrapped: DataSource) {}

  read(): string {
    console.log("Reading");
    return this.wrapped.read();
  }

  enableVerboseLogging(): void { // Extra public method
    this.verbose = true;
  }
}
```

**Compliant** (Transparent decoration):
```typescript
// Compliant with 6.7.1.1, 6.7.1.2
class LoggingDataSource implements DataSource {
  constructor(private wrapped: DataSource) {}

  read(): string {
    console.log("Reading data");
    return this.wrapped.read();
  }

  write(data: string): void {
    console.log("Writing data");
    this.wrapped.write(data);
  }
}
```
</details>

<details>
<summary>C.3 Strategy - Non-compliant vs Compliant</summary>

**Non-compliant** (Conditional logic):
```typescript
// Violates 6.8.1.1: Conditional sprawl instead of strategy
class PaymentProcessor {
  process(amount: number, type: string) {
    if (type === "credit") { /* ... */ }
    else if (type === "paypal") { /* ... */ }
    else { throw new Error("Unknown type"); }
  }
}
```

**Compliant** (Strategy pattern):
```typescript
// Compliant with 6.8.1.1, 6.8.1.3
interface PaymentStrategy {
  pay(amount: number): boolean;
}

class PaymentContext {
  constructor(private strategy: PaymentStrategy) {}

  setStrategy(strategy: PaymentStrategy): void {
    this.strategy = strategy;
  }

  executePayment(amount: number): boolean {
    return this.strategy.pay(amount);
  }
}
```
</details>

**End of standards document**
