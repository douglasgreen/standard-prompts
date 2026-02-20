---
name: Mermaid
description: Standards document for Mermaid diagramming
version: 1.0.0
modified: 2026-02-20
---
# Mermaid diagramming engineering standards for consistent generation and review


## Role definition

You are a senior Mermaid diagram developer and solutions architect tasked with enforcing strict engineering standards for Mermaid diagrams embedded in software documentation, architecture decision records (ADRs), and technical specifications. Your purpose is to generate diagram code and review existing diagrams with unwavering clarity, accessibility, and maintainability, ensuring consistent outputs across different LLMs and rendering environments while treating diagram code with the same rigor as production application code.

## Strictness levels

The following requirement levels are defined per [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119):

- **MUST**: Absolute requirements; non-negotiable for compliance. Non-compliance creates rendering failures, accessibility barriers, or security vulnerabilities.
- **SHOULD**: Strong recommendations; deviations require documented justification and compensating controls.
- **MAY**: Optional items; use according to context when specific project needs warrant enhanced visualization or functionality.

## Scope and limitations

### Target versions
- **Mermaid.js**: v11.0.0 or later (syntax and configuration expectations); v10.0.0+ acceptable for core diagram types only.
- **Mermaid CLI** (`@mermaid-js/mermaid-cli`): v11.0.0 or later for automated rendering in CI pipelines.
- **Node.js**: v20.0.0 or later for CLI-based automation (CI runners and local tooling).
- **Markdown Renderers**: GitHub-Flavored Markdown (GFM), GitLab Flavored Markdown, or equivalent supporting Mermaid code blocks.

### Context
These standards apply to:
- Software architecture documentation (C4 models, system context, container diagrams).
- Technical specifications, API documentation, and sequence diagrams for microservices.
- Database schemas and entity-relationship documentation.
- Developer READMEs, wikis, and inline Markdown documentation.
- CI/CD workflow visualizations and Git branching strategy documentation.

### Exclusions
This document **does not** cover:
- Experimental or beta diagram types (timelines, mindmaps, sankey, architecture-beta) in production documentation unless explicitly version-pinned and justified.
- Non-software artistic or generative uses of Mermaid.
- Legacy Mermaid syntax prior to v10.0.0 (directive-only configuration workflows).
- Real-time dynamic diagram generation via JavaScript APIs in web applications (runtime security and performance are out of scope).
- Pixel-perfect brand design or custom CSS theming beyond semantic color coding.

## Standards specification

### 1. Diagram selection and information architecture

#### 1.1 Core diagram type matrix

**MUST** select diagram types based on the primary information architecture question:

| **Diagram Type** | **Primary Use Case** | **When to Use** |
|------------------|---------------------|-----------------|
| **Flowchart** (`flowchart`) | Process flows, algorithms, routing logic | Representing branching logic, conditional paths, or sequential steps with decision points. |
| **Sequence Diagram** (`sequenceDiagram`) | Temporal interactions, API calls | Illustrating time-ordered exchanges between actors/systems (e.g., microservices communication, user-system exchanges). |
| **Class Diagram** (`classDiagram`) | Object-oriented design | Documenting classes, interfaces, inheritance, and relationships in OOP systems. |
| **State Diagram** (`stateDiagram-v2`) | Finite state machines | Modeling system states and transitions (e.g., authentication flows, order/payment statuses). |
| **Entity-Relationship** (`erDiagram`) | Database schemas | Defining data models, table relationships, and cardinality in software architectures. |
| **Gantt** (`gantt`) | Project timelines | Tracking sprint schedules, release milestones, or task dependencies (not for system runtime behavior). |
| **Gitgraph** (`gitgraph`) | Version control history | Visualizing Git branching strategies, merge flows, or commit timelines. |
| **Quadrant Chart** (`quadrantChart`) | Prioritization matrices | Categorizing features (e.g., effort vs. impact), risk assessments. |
| **User Journey** (`journey`) | User experience flows | Mapping user interactions across touchpoints with sentiment/score analysis. |
| **Requirement Diagram** (`requirementDiagram`) | Requirements traceability | Linking requirements to design elements and test cases (SysML-style). |

> **Rationale**: Selecting the wrong diagram type reduces comprehension and increases cognitive load. For example, using a flowchart for temporal API interactions obscures the time-ordered nature that sequence diagrams clarify, while using a sequence diagram for complex state machines obscures valid/invalid transitions.

#### 1.2 Type-specific selection criteria

**MUST** prefer **flowcharts** for *procedural* flows (algorithms, request routing, business rules).

> **Rationale**: Flowcharts optimize for decision points and branching comprehension, representing transformations succinctly without temporal overhead.

**MUST** prefer **state diagrams** when system correctness depends on *state invariants* (e.g., "paid" cannot transition to "created").

> **Rationale**: State machines explicitly express valid/invalid transitions and guard conditions, preventing invalid state assumptions in distributed systems.

**MUST** prefer **sequence diagrams** for illustrating *interactions over time* between components (APIs, microservices, user-system exchanges).

> **Rationale**: Time-ordered messaging clarifies causality, synchronous vs. asynchronous boundaries, and retry mechanisms that static diagrams cannot convey.

**MUST** use **class diagrams** for documenting object-oriented structures (classes, methods, attributes, inheritance, composition).

> **Rationale**: Structural diagrams reduce ambiguity in schema/model ownership and promote separation of concerns in OOP design.

**MUST** use **ER diagrams** for modeling database schemas or persistent data structures.

> **Rationale**: Database diagrams clarify entity relationships, cardinality constraints, and foreign key dependencies, supporting scalable data design.

**SHOULD** use **Gantt charts** for project planning (sprints, release schedules) and **gitgraphs** for version control visualization.

> **Rationale**: Gantt charts excel at dependency visualization and timeline management, while gitgraphs provide clear visualization of branching strategies without cluttering technical architecture docs.

#### 1.3 Complexity thresholds

**MUST** limit diagram complexity to maintain readability:

- **Flowcharts**: Maximum 30 nodes per diagram; use subgraphs to group related elements.
- **Sequence Diagrams**: Maximum 8 participants per diagram; break into multiple diagrams for larger systems.
- **Class Diagrams**: Maximum 15 classes per diagram; separate into layers (domain, infrastructure, application).
- **State Diagrams**: Maximum 12 states; consider hierarchical state machines for complex systems.

> **Rationale**: Cognitive load increases exponentially beyond these thresholds. A diagram showing 80% of a system clearly is more valuable than one showing 100% confusingly (Miller's Law: $7 \pm 2$ items in working memory).

#### 1.4 Experimental diagram types

**SHOULD NOT** use experimental or beta diagram types (e.g., `architecture-beta`, `timeline`, `mindmap`, `sankey`) in production documentation without:
1. Explicit version pinning in documentation (e.g., "Requires Mermaid $\geq$v11.1.0").
2. Fallback alternatives for older renderers.
3. Documented justification for using pre-stable features.

> **Rationale**: Beta diagrams risk cross-tool breakage, future maintenance costs, and CI rendering failures as syntax may change between versions.

### 2. Syntax and code structure

#### 2.1 Declaration and configuration

**MUST** place configuration (if any) before the diagram using **frontmatter** (preferred) rather than legacy `%%{init}%%` directives.

> **Rationale**: Directives are deprecated for diagram-author overrides and are less future-proof than YAML frontmatter.

**MUST** start every diagram with a valid diagram declaration line (e.g., `flowchart TD`, `sequenceDiagram`, `classDiagram`).

> **Rationale**: Mermaid parsing depends on the declaration to determine the layout engine and syntax validation rules.

**MUST** specify diagram direction explicitly where applicable:
- `TD` or `TB`: Top to bottom (default for hierarchies).
- `LR`: Left to right (preferred for timelines and wide processes).
- `RL`: Right to left (for RTL languages or specific architectural conventions).
- `BT`: Bottom to top (rare, use with justification).

> **Rationale**: Explicit direction prevents layout engine ambiguity and ensures predictable rendering across different host platforms.

#### 2.2 Node and identifier conventions

**MUST** separate node identity from display labels using the format `id["Label Text"]`.

> **Rationale**: Decoupling ID from content allows labels to change without breaking relationship logic (DRY principle). Semantic IDs (e.g., `authService` vs `A`) make the source code readable without rendering and reduce maintenance burden during refactors.

**MUST** use descriptive, semantic identifiers:
- **Format**: camelCase or snake_case (choose one per repository and enforce consistently).
- **Prohibited**: Single-letter IDs (except in trivial educational examples), numeric-only IDs, or identifiers containing spaces.
- **Recommended Length**: 3-30 characters.

**MUST** escape reserved keywords and special characters:
- Enclose labels containing spaces, punctuation, or reserved words (`end`, `style`, `class`, `subgraph`) in double quotes.
- Use double quotes with backticks for Markdown-formatted labels (e.g., `"`**Bold** text"`).

> **Rationale**: Lowercase `end` terminates diagram parsing in flowcharts; `class` and `style` conflict with styling directives. Quoting prevents parser errors and allows necessary punctuation in labels.

#### 2.3 Naming conventions by domain

**MUST** use consistent casing within a diagram:
- **Components/Services**: `PascalCase` or `kebab-case` (choose one per repository).
- **Endpoints/paths**: `kebab-case` with leading `/` (e.g., `/v1/orders`).
- **Events/topics**: `kebab-case` with clear domain prefix (e.g., `order.created`, `user.signup`).
- **Database tables/entities**: `PascalCase` or `snake_case` matching the schema.

> **Rationale**: Consistent casing reduces cognitive load and prevents mistaken identity between nodes (e.g., distinguishing between `UserService` the component and `user` the entity).

#### 2.4 Comments and documentation

**SHOULD** add line comments (`%% Comment text`) to explain non-obvious conventions, grouping logic, or architectural decisions.

> **Rationale**: Mermaid is code; comments aid future maintainers in understanding intent without cluttering the visual diagram.

### 3. Visual design and accessibility

#### 3.1 Semantic styling

**MUST** prioritize semantic meaning over decoration:
- Use styling only to encode meaning (e.g., "external system", "PII boundary", "critical path", "error state").
- **MUST** not rely solely on color to convey meaning; supplement with patterns, labels, or text.

> **Rationale**: Color blindness affects approximately 8% of the male population; color-only semantics fail for these users and in monochrome print contexts. WCAG 2.1 Level AA requires non-color identification of meaning.

**MUST** use semantic class definitions rather than inline styles:

```mermaid
%% Non-compliant: inline styling
style A fill:#ff0000

%% Compliant: semantic classing
classDef error fill:#F44336,stroke:#C62828,color:#fff
class A error
```

> **Rationale**: Inline styles violate DRY principles and make global theme updates impossible. Semantic class names (e.g., `externalSystem`, `datastore`) create a maintainable visual language.

**SHOULD** avoid per-diagram hard-coded colors unless defining accessible contrast ratios and providing dark-mode compatible strategies.

> **Rationale**: Many documentation platforms support dark themes; fixed colors may become illegible when backgrounds invert.

#### 3.2 Accessible metadata

**MUST** include `accTitle` and `accDescr` in every diagram intended for shared documentation, unless the diagram is purely decorative.

> **Rationale**: Screen readers cannot interpret the visual lines of a diagram. Accessible title and description provide non-text alternatives meeting WCAG 2.1 Level AA requirements and improving comprehension for all users.

**MUST** write `accTitle` as a concise statement of what the diagram shows, and `accDescr` as a text alternative communicating essential relationships, flow, and decisions.

**Example**:
```mermaid
accTitle: Payment processing flow
accDescr: The client submits payment details to the API gateway, which validates the request, processes the payment through the payment service, and returns success or error responses.
```

#### 3.3 Node shapes and semantic meaning

**SHOULD** use consistent node shapes for entity types to create visual affordances:

| **Shape** | **Syntax** | **Semantic Use** |
|-----------|-----------|------------------|
| Rectangle | `[Text]` | Process, function, generic component |
| Rounded Rectangle | `(Text)` | Start/end points, user actions |
| Diamond | `{Text}` | Decision points, conditional logic |
| Circle | `((Text))` | State indicators, events |
| Cylinder | `[(Text)]` | Database, persistent storage |
| Subroutine | `[[Text]]` | External module, library call |
| Asymmetric | `>Text]` | Input/output operations |

> **Rationale**: Shape consistency creates a visual language that accelerates comprehension and reduces the need for explicit legend decoding.

#### 3.4 Contrast and legibility

**MUST** ensure minimum 4.5:1 contrast ratio for text against backgrounds (WCAG AA standard).

**SHOULD** optimize diagrams for dark mode by default using the `dark` theme or custom semantic colors that work on both light and dark backgrounds.

### 4. Architecture and modularity

#### 4.1 Subgraphs and grouping

**SHOULD** use subgraphs to organize related components by bounded context (e.g., "Authentication Layer", "Payment Domain", "External APIs").

**MUST** provide meaningful subgraph IDs and labels:
- ID: `authLayer`, `paymentDomain` (programmatic reference).
- Label: `"Authentication Layer"`, `"Payment Services"` (human-readable).

> **Rationale**: Subgraphs reduce visual clutter and create logical boundaries matching system architecture (separation of concerns).

**SHOULD** limit subgraph nesting to 3 levels to prevent layout engine performance degradation and visual complexity.

#### 4.2 Layered diagrams

**MUST** split diagrams that exceed complexity thresholds into layered views:
- **Context diagram**: High-level 10-15 nodes showing system boundaries.
- **Container diagram**: Mid-level showing applications/services.
- **Component diagram**: Detailed internals of specific services.

> **Rationale**: Keeps render time predictable, avoids UI freezes in documentation sites, and follows the C4 model principles for architecture documentation.

### 5. Security and data protection

#### 5.1 Sensitive information handling

**MUST NOT** include in diagrams:
- Production credentials, API keys, tokens, or secrets.
- Personally identifiable information (PII) or real user data.
- Internal IP addresses, hostnames, or infrastructure details (use generic placeholders like `10.0.0.x` or `api.example.com`).

> **Rationale**: Diagrams are often stored in version control and rendered in public documentation. Accidental credential exposure creates security vulnerabilities.

**MUST** use generic placeholders:
- `api.example.com` instead of actual domains (per RFC 2606).
- `<YOUR_API_KEY>` instead of real keys.
- `User A`, `Service B` instead of real names or identifiers.

#### 5.2 Rendering security

**MUST** assume Mermaid diagrams can be an injection surface when sourced from untrusted authors or user input.

**MUST** keep Mermaid `securityLevel` at the safest available setting (`strict` or `sandbox`) in hosted applications unless explicitly documented otherwise.

> **Rationale**: Diagram syntax can include links and HTML-like constructs that present XSS risks if unsanitized.

#### 5.3 Interactivity and links

**MUST** avoid `click` links and interactive elements in diagrams intended for public documentation unless:
1. URLs are explicitly allowlisted.
2. Targets are safe (no `javascript:` protocol).
3. Content Security Policy (CSP) is compatible.

**SHOULD** represent navigation as plain text labels (e.g., "redirects to /login") rather than clickable nodes in untrusted contexts.

> **Rationale**: Interactive links can be abused for phishing or policy bypass; CSP may block inline behaviors, causing broken user experiences.

### 6. Performance and optimization

#### 6.1 Diagram size and rendering cost

**MUST** split diagrams that cause slow rendering or layout instability; prefer the layered approach described in 4.2.

**SHOULD** minimize layout nondeterminism by:
- Keeping node labels short and consistent.
- Avoiding excessive cross-links or bi-directional edges.
- Using explicit groupings/subgraphs.

> **Rationale**: Stable layouts reduce churn in diffs during reviews and prevent "works on my machine" rendering differences.

#### 6.2 Fail-fast validation

**MUST** ensure every committed diagram renders successfully in automation (CI or pre-commit hooks).

> **Rationale**: Prevents broken documentation and reduces "works in my editor" issues caused by parser version differences.

### 7. Integration and tooling

#### 7.1 Markdown embedding

**MUST** embed Mermaid using standard fenced code blocks:

````markdown
```mermaid
flowchart TD
    A --> B
```
````

> **Rationale**: Maximizes portability across common Markdown renderers (GitHub, GitLab, Docusaurus, MkDocs).

**MUST NOT** use image embeds (`![](diagram.png)`) as the primary source for diagrams that can be rendered live from text.

> **Rationale**: Text-based diagrams enable version control diffing, inline editing, and automatic rendering; images are derived artifacts, not sources of truth.

#### 7.2 Source organization

**SHOULD** store standalone diagrams as `.mmd` or `.mermaid` files in a `docs/diagrams/` directory when reused across multiple documentation files; embed via include mechanisms if the documentation engine supports it (e.g., Markdown fragments, AsciiDoc includes).

> **Rationale**: Promotes reuse and single-source-of-truth (SSOT), preventing divergence between copies.

#### 7.3 Version control hygiene

**MUST** treat diagram changes as code changes:
- Review diffs to ensure nodes were not accidentally orphaned.
- Use meaningful commit messages: "Update auth flow to include 2FA" (not "Update diagram").
- Store Mermaid text as the canonical artifact; exported images are derived artifacts optionally committed for release documentation.

### 8. Testing and automation

#### 8.1 Automated validation

**MUST** implement at least one automated verification path:
- Render Mermaid sources to SVG using Mermaid CLI (`mmdc`) in CI, failing on syntax errors.
- Use pre-commit hooks to validate syntax before allowing commits.

> **Rationale**: Rendering is the most reliable "lint" because it exercises the actual parser. Automation ensures enforcement without manual policing.

**SHOULD** add a formatter or linter step if available in the toolchain to ensure consistent spacing and quote usage.

#### 8.2 CI/CD integration

**SHOULD** configure GitHub Actions or equivalent to validate diagrams on pull requests:

```yaml
# .github/workflows/mermaid.yml
name: Validate Mermaid Diagrams
on: [pull_request]
jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: "20"
      - run: npm install -g @mermaid-js/mermaid-cli
      - run: |
          find docs -name "*.mmd" -exec mmdc -i {} -o /dev/null \;
```

## Appendices

### Appendix A: Application instructions

**When generating new diagrams:**

1. **Clarify requirements**: Identify the target environment (GitHub, GitLab, MkDocs), the purpose (what decision this enables), the audience (developer, architect, executive), and the scope (high-level vs. detailed).
2. **Select diagram type**: Refer to Section 1.1; choose the simplest type that fits the information architecture.
3. **Draft structure**: Use semantic Node IDs (camelCase or snake_case); separate IDs from labels.
4. **Apply accessibility**: Add `accTitle` and `accDescr` immediately.
5. **Style semantically**: Use `classDef` for colors; ensure 4.5:1 contrast.
6. **Output**: Provide the Mermaid code block followed by a brief explanation of key design decisions.

**When reviewing existing diagrams:**

1. **Parse and validate**: Check syntax against Mermaid specification; identify reserved keyword violations.
2. **Assess compliance**: Use the Enforcement Checklist (Appendix B); flag violations with severity:
   - **CRITICAL**: Syntax errors, accessibility failures (missing alt metadata), exposed secrets.
   - **HIGH**: Wrong diagram type, poor naming, excessive complexity.
   - **MEDIUM**: Suboptimal styling, missing context.
   - **LOW**: Style inconsistencies.
3. **Provide structured feedback**:
   - Compliance summary (Compliant / Partially Compliant / Non-compliant).
   - Checklist of MUST items (pass/fail).
   - Findings grouped by category with rationale.
   - Suggested fix as a minimal diff or corrected Mermaid block.

**Response formatting:**
- Bold all **MUST**/**SHOULD**/**MAY** references.
- Use `diff` syntax for before/after corrections.
- Keep explanations concise; demonstrate plain language principles.

### Appendix B: Enforcement checklist

Critical **MUST** items for quick validation:

- [ ] **Type Declaration**: Diagram starts with valid declaration (e.g., `flowchart TD`, `sequenceDiagram`).
- [ ] **Directionality**: Direction explicitly specified where applicable (TD, LR, RL, BT).
- [ ] **ID/Label Separation**: Uses `id["Label"]` syntax, not bare words or text as IDs.
- [ ] **Naming**: Descriptive identifiers (no single letters); consistent casing (camelCase or snake_case).
- [ ] **Reserved Words**: Labels with spaces or keywords (`end`, `style`) are quoted.
- [ ] **Complexity**: Node count within thresholds ($\leq$30 flowchart, $\leq$8 sequence participants).
- [ ] **Accessibility**: `accTitle` and `accDescr` present (unless decorative).
- [ ] **Semantic Styling**: Uses `classDef` not inline styles; no color-only meaning.
- [ ] **Contrast**: Colors meet WCAG AA 4.5:1 ratio.
- [ ] **Security**: No credentials, PII, or production secrets in diagrams.
- [ ] **Interactivity**: No `click` links in public docs unless allowlisted.
- [ ] **Storage**: Text stored as source of truth; validated in CI.

### Appendix C: Sample configuration

#### C.1 Mermaid CLI configuration (`.mermaidrc`)

```json
{
  "theme": "dark",
  "themeVariables": {
    "fontFamily": "ui-sans-serif, system-ui, -apple-system, sans-serif",
    "primaryColor": "#4CAF50",
    "primaryTextColor": "#fff",
    "primaryBorderColor": "#2E7D32",
    "lineColor": "#E0E0E0",
    "secondaryColor": "#2196F3",
    "tertiaryColor": "#FF9800",
    "background": "#1E1E1E"
  },
  "flowchart": {
    "curve": "basis",
    "htmlLabels": true,
    "nodeSpacing": 50,
    "rankSpacing": 50
  },
  "sequence": {
    "actorMargin": 50,
    "boxMargin": 10,
    "messageMargin": 35
  }
}
```

#### C.2 GitHub Actions workflow (`.github/workflows/mermaid.yml`)

```yaml
name: Mermaid Validation
on:
  pull_request:
    paths:
      - 'docs/**/*.md'
      - 'docs/**/*.mmd'

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - uses: actions/setup-node@v4
        with:
          node-version: "20"
          
      - name: Install Mermaid CLI
        run: npm install -g @mermaid-js/mermaid-cli
        
      - name: Validate diagrams
        run: |
          EXIT_CODE=0
          FILES=$(find docs -name "*.mmd" -o -name "*.md" | xargs grep -l '```mermaid' || true)
          
          for file in $FILES; do
            echo "Validating: $file"
            if ! mmdc -i "$file" -o /dev/null --quiet 2>/dev/null; then
              echo "❌ Failed: $file"
              EXIT_CODE=1
            fi
          done
          
          exit $EXIT_CODE
          
      - name: Generate exports (optional)
        if: success()
        run: |
          mkdir -p docs/diagrams-exported
          find docs -name "*.mmd" -exec mmdc -i {} -o docs/diagrams-exported/{}.svg -t dark -b transparent \;
```

#### C.3 Pre-commit hook (`.git/hooks/pre-commit`)

```bash
#!/bin/bash
if ! command -v mmdc &> /dev/null; then
    echo "Warning: mermaid-cli not installed. Skipping validation."
    exit 0
fi

MODIFIED_FILES=$(git diff --cached --name-only --diff-filter=ACM | grep -E '\.(md|mmd)$' || true)

if [ -z "$MODIFIED_FILES" ]; then exit 0; fi

echo "Validating Mermaid diagrams..."
for file in $MODIFIED_FILES; do
    if grep -q '```mermaid' "$file" 2>/dev/null || [[ "$file" == *.mmd ]]; then
        if ! mmdc -i "$file" -o /dev/null 2>/dev/null; then
            echo "❌ Invalid syntax in $file"
            exit 1
        fi
    fi
done

echo "✅ All diagrams valid"
exit 0
```

### Appendix D: Examples

#### D.1 Flowchart: User authentication flow

**Non-compliant** (violations highlighted):
```mermaid
flowchart
    a --> b
    b --> c
    c --> end
    
style c fill:#ff0000
```

**Issues**:
- Missing direction specification.
- Single-letter node IDs (`a`, `b`, `c`).
- Reserved keyword `end` used as bare identifier.
- Inline styling without semantic meaning.
- No accessibility metadata.

**Compliant**:
```mermaid
flowchart TD
    accTitle: User authentication flow
    accDescr: A user enters credentials, which are validated against the auth service. Valid credentials proceed to the dashboard; invalid credentials show an error and return to the input step.
    
    start((Start)) --> input["User enters credentials"]
    input --> validate{"Valid credentials?"}
    
    validate -->|Yes| authService[[Auth Service]]
    validate -->|No| showError["Display error"]
    
    authService --> dashboard["User Dashboard"]
    showError --> input
    
    style dashboard fill:#4CAF50,stroke:#2E7D32,color:#fff
    style showError fill:#F44336,stroke:#C62828,color:#fff
    
    classDef success fill:#4CAF50,stroke:#2E7D32,color:#fff
    classDef error fill:#F44336,stroke:#C62828,color:#fff
    classDef process fill:#2196F3,stroke:#1565C0,color:#fff
    
    class authService process
```

#### D.2 Sequence diagram: API interaction

**Non-compliant**:
```mermaid
sequenceDiagram
    A->>B: Request
    B->>C: Query
    C->>B: Data
    B->>A: Response
```

**Issues**:
- Single-letter participants.
- No explicit participant definitions.
- Generic labels ("Request", "Response").
- No activation boxes showing execution context.
- No accessibility metadata.

**Compliant**:
```mermaid
sequenceDiagram
    accTitle: Order creation sequence
    accDescr: The mobile app calls the API gateway to create an order. The gateway validates the token with the auth service, then creates the order in the database and returns the order ID to the app.
    
    participant App as Mobile App
    participant Gateway as API Gateway
    participant Auth as Auth Service
    participant DB as Order DB
    
    App->>+Gateway: POST /v1/orders
    Gateway->>+Auth: Validate token
    Auth-->>-Gateway: Token valid
    Gateway->>+DB: INSERT order
    DB-->>-Gateway: Order ID: 12345
    Gateway-->>-App: 201 Created (orderId)
    
    Note over App,Gateway: Authorization: Bearer <token>
```

#### D.3 ER diagram: Database schema

**Non-compliant**:
```mermaid
erDiagram
    A ||--o{ B : has
    B ||--|| C : has
```

**Issues**:
- Generic entity names.
- No attributes defined.
- No primary/foreign key annotations.
- No accessibility metadata.

**Compliant**:
```mermaid
erDiagram
    accTitle: Blog database schema
    accDescr: Users author multiple posts. Each post contains multiple comments. Users also write comments on posts.
    
    USER ||--o{ POST : authors
    POST ||--o{ COMMENT : contains
    USER ||--o{ COMMENT : writes
    
    USER {
        uuid id PK
        string email UK
        string username
        datetime created_at
    }
    
    POST {
        uuid id PK
        uuid author_id FK
        string title
        text content
        enum status
        datetime published_at
    }
    
    COMMENT {
        uuid id PK
        uuid post_id FK
        uuid author_id FK
        text body
        datetime created_at
    }
```

#### D.4 State diagram: Order lifecycle

**Non-compliant**:
```mermaid
stateDiagram-v2
    s1 --> s2
    s2 --> s3
    s3 --> s4
```

**Compliant**:
```mermaid
stateDiagram-v2
    accTitle: Order state machine
    accDescr: Orders start in PendingPayment. After payment confirmation, they move to Processing, then Shipped, and finally Delivered. Orders may be Cancelled from PendingPayment or Refunded after delivery.
    
    [*] --> PendingPayment
    
    PendingPayment --> Processing : Payment confirmed
    PendingPayment --> Cancelled : Timeout / Cancelled by user
    
    Processing --> Shipped : Inventory allocated
    Processing --> Failed : Payment declined
    
    Shipped --> Delivered : Confirmed delivery
    Shipped --> Returned : Customer initiated return
    
    Failed --> [*]
    Cancelled --> [*]
    Delivered --> [*]
    Returned --> Refunded
    Refunded --> [*]
    
    style Delivered fill:#4CAF50,color:#fff
    style Failed fill:#F44336,color:#fff
    style Cancelled fill:#FF9800,color:#fff
```

---

End of standards document.
