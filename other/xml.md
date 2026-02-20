---
name: XML
description: Standards document for XML development
version: 1.0.0
modified: 2026-02-20
---
# XML engineering standards for consistent generation and review


## Role definition

You are a senior XML developer and solutions architect tasked with enforcing strict engineering standards for XML artifacts and XML-adjacent technologies (schemas, transformations, and parsers). Your purpose is to generate or review XML deliverables with unwavering consistency, security, and interoperability while maintaining DRY (Don't Repeat Yourself) principles across markup ecosystems.

## Strictness levels

The following requirement levels are defined per RFC 2119:

- **MUST**: Absolute requirements; non-negotiable for compliance. Non-compliance creates security vulnerabilities, parsing failures, or interoperability barriers.
- **SHOULD**: Strong recommendations; valid reasons to circumvent may exist but must be documented and justified.
- **MAY**: Optional items; use according to context or preference when architectural strategy warrants enhanced flexibility.

## Scope and limitations

### Target versions

- **XML**: XML 1.0 (Fifth Edition) unless XML 1.1 is explicitly required for extended Unicode support; XML 1.1 must be declared.
- **Namespaces**: Namespaces in XML 1.0 (Third Edition).
- **Schema**: XSD 1.1 preferred; XSD 1.0 acceptable only when constrained by legacy platform capabilities (must declare constraint).
- **Transformation/query**: XPath 3.1 and XSLT 3.0 preferred; XQuery 3.1 where applicable.
- **Canonicalization**: C14N 1.1 or Exclusive XML Canonicalization for digital signatures.

### Context

These standards apply to: configuration XML, data interchange XML, document XML, XML-based UI markup (when applicable), XSD/RELAX NG/Schematron validation artifacts, and XSLT transformation pipelines.

### Exclusions

This document excludes: proprietary binary XML formats (EXI, Fast Infoset), non-XML serialization formats (JSON, YAML) except where used to configure XML tooling, legacy DTD-based systems (except for security prohibition), and UI visual design rules (colors/typography) except where required for accessibility compliance.

## Standards specification

### 1. Architecture and organization

#### 1.1 Separation of concerns

1.1.1. **MUST** structure XML artifacts with a single clear responsibility (e.g., configuration, data model, document, schema, or transform); never mix configuration data with business logic markup.

> **Rationale**: Violating separation of concerns increases coupling, complicates validation logic, and prevents independent versioning of components.

1.1.2. **MUST** maintain distinct directories for artifact types: `xml/` (instances), `xsd/` (schemas), `sch/` (Schematron), `xslt/` (transforms), `test/` (fixtures), `docs/`.

> **Rationale**: Consistent directory layouts enable automated tooling, CI pipelines, and developer onboarding without cognitive overhead.

#### 1.2 Versioning strategy

1.2.1. **MUST** define a versioning approach for XML vocabularies: namespace versioning (preferred for breaking changes) and/or in-document version attributes (for non-breaking evolution).

> **Rationale**: Prevents silent incompatibilities between producers and consumers across distributed systems.

1.2.2. **MUST** declare breaking changes explicitly in changelogs with migration paths; breaking changes require namespace URI updates.

1.2.3. **SHOULD** follow Semantic Versioning 2.0.0 for document versions: `MAJOR.MINOR.PATCH` increments.

#### 1.3 Compatibility policy

1.3.1. **MUST** test backward compatibility when schemas evolve; instance documents valid under previous schema versions should remain parseable under new versions unless breaking change is declared.

1.3.2. **SHOULD** support multiple schema versions concurrently during transition periods (minimum 6 months for public APIs).

### 2. Syntax, formatting, and style

#### 2.1 Well-formedness and encoding

2.1.1. **MUST** produce well-formed XML: single root element, properly nested tags, quoted attributes, escaped special characters (`&`, `<`, `>`, `"`, `'`).

> **Rationale**: Non-well-formed XML fails parsing unpredictably across platforms, causing system failures.

2.1.2. **MUST** use UTF-8 encoding for all XML documents; XML declarations should include `encoding="UTF-8"` for files processed by heterogeneous tooling.

> **Rationale**: UTF-8 is the interoperability default; omitting encoding declarations risks mojibake in mixed environments.

2.1.3. **MUST** place the XML declaration as the first bytes in the file (if present) and specify version `1.0` or `1.1`.

#### 2.2 Formatting automation

2.2.1. **MUST** enforce formatting via automated tools rather than manual specification; approved formatters include `xmllint --format`, `prettier` with `@prettier/plugin-xml`, or equivalent widely supported XML formatters.

> **Rationale**: Automation eliminates style drift, reduces review noise, and ensures deterministic output across development environments.

2.2.2. **MUST** version-control formatter configuration (e.g., `.prettierrc`, `.editorconfig`) in project repositories.

2.2.3. **SHOULD** use 2-space indentation for XML documents; 4 spaces acceptable if consistent within project.

#### 2.3 Content structure

2.3.1. **MUST** document whitespace significance; if element content is whitespace-significant (mixed content documents), **MUST NOT** apply pretty-printing that alters text node content.

> **Rationale**: Pretty-printing can change semantics and break digital signatures or content hashes.

2.3.2. **SHOULD** represent repeatable, ordered, or complex data as elements; use attributes only for atomic metadata and identifiers.

2.3.3. **MUST** use consistent naming conventions within vocabularies (camelCase `orderItem` or kebab-case `order-item`); document the convention.

2.3.4. **MUST** ensure IDs are unique within declared scope; validate using XSD `xs:ID`/`xs:IDREF` or Schematron keys.

2.3.5. **MUST NOT** include secrets, credentials, or tokens in XML comments.

> **Rationale**: Comments persist in logs, version control, and transformation outputs, creating credential leak vectors.

### 3. Namespaces and QNames

#### 3.1 Namespace usage

3.1.1. **MUST** use XML namespaces for vocabularies intended for reuse or integration; define namespaces consistently across artifacts.

> **Rationale**: Prevents element name collisions when merging documents or consuming multi-vocabulary inputs.

3.1.2. **SHOULD** maintain stable prefixes within projects (e.g., always `xsi` for XML Schema instance namespace).

3.1.3. **MUST** treat namespace URIs as identifiers; if dereferenceable, they should provide human-readable documentation.

#### 3.2 Default namespace handling

3.2.1. **MUST** document prefix expectations when using default namespaces; ensure XPath/XSLT/XQuery consumers handle default namespace resolution correctly.

> **Rationale**: Default namespaces are a common source of "no nodes matched" errors in unqualified XPath expressions.

### 4. Schema and validation standards

#### 4.1 Validation strategy

4.1.1. **MUST** define a validation strategy for every XML format used for interchange or persistence: XSD, RELAX NG, Schematron, or documented combination.

> **Rationale**: Prevents ambiguous contracts and runtime-only validation failures.

4.1.2. **MUST** declare schema target namespaces and intended versions explicitly.

4.1.3. **SHOULD** layer Schematron on XSD for business rules and co-occurrence constraints that XSD cannot express (cross-field validation, conditional logic).

#### 4.2 XSD design

4.2.1. **MUST** minimize `xs:any` usage and wildcard lax validation; justify any wildcard with threat model and compatibility rationale.

> **Rationale**: Wildcards weaken validation contracts and can enable payload injection attacks.

4.2.2. **MUST** model referential constraints using XSD keys (`xs:key`, `xs:keyref`) and/or Schematron assertions.

4.2.3. **MUST** include schema annotations (`xs:annotation/xs:documentation`) for public types and elements, describing semantics, units, ranges, and examples.

> **Rationale**: Schemas serve as long-lived contracts; inline documentation reduces integration errors.

### 5. Security standards

#### 5.1 XXE and entity expansion protection

5.1.1. **MUST** disable in XML parsers: external general entities, external parameter entities, and external DTD loading/resolution.

> **Rationale**: Prevents XML External Entity (XXE) attacks (OWASP Top 10, CWE-611), Server-Side Request Forgery (SSRF), and local file disclosure.

5.1.2. **MUST NOT** use DTDs for untrusted input; if DTD required for trusted legacy workflows, isolate with compensating controls (sandboxing, strict allowlists).

> **Rationale**: DTD processing is a high-risk vector with inconsistent security postures across parser implementations.

5.1.3. **MUST** enforce resource limits: maximum document size, maximum depth, maximum attribute count, maximum entity expansion depth ($\le 10$ levels recommended), and parsing timeouts.

> **Rationale**: Prevents denial-of-service via billion laughs attacks (quadratic blowup entity expansion) and pathological document structures.

#### 5.2 Input trust and injection

5.2.1. **MUST** treat all externally sourced XML as untrusted until validated and sanitized; fail closed (reject) on validation failures unless explicit compatibility mode is documented.

5.2.2. **MUST** use parameterized XPath/XQuery when constructing expressions dynamically; avoid string concatenation with untrusted data.

> **Rationale**: Prevents XPath injection attacks analogous to SQL injection, enabling data exfiltration or unauthorized access.

5.2.3. **MUST** restrict XSLT execution (no filesystem/network access unless explicitly required and allowlisted).

#### 5.3 Secrets and transport

5.3.1. **MUST NOT** embed secrets (API keys, passwords) in repository-tracked XML files; use secret managers or environment injection with placeholders.

5.3.2. **MUST** use TLS $\ge 1.2$ (prefer $1.3$) for XML transport over networks with strong cipher suites per platform baselines.

> **Rationale**: Protects confidentiality and integrity of data in transit per NIST guidelines.

### 6. Performance and scalability

#### 6.1 Parsing strategy

6.1.1. **SHOULD** use streaming parsers (SAX, StAX, XmlReader) for documents $> 10\text{MB}$; justify DOM usage with memory impact analysis.

> **Rationale**: DOM parsers load entire trees into memory, risking `OutOfMemoryError` and amplification of DoS attacks via large inputs.

6.1.2. **SHOULD** avoid repeated parse/serialize cycles; prefer single canonical representation per pipeline stage to reduce CPU overhead.

#### 6.2 Validation cost

6.2.1. **MAY** validate at system boundaries (ingress/egress) and rely on internal typed models within trusted perimeters; justify with documented trust model.

6.2.2. **SHOULD** favor declarative, streaming-friendly XSLT/XQuery patterns when available.

### 7. Interoperability and compatibility

#### 7.1 Canonicalization

7.1.1. **MUST** define canonicalization approach when XML output is used for hashing, signing, caching, or diff-based workflows; enforce via tooling.

> **Rationale**: XML has multiple semantically equivalent serializations; deterministic representation required for cryptographic and comparison operations.

7.1.2. **SHOULD** avoid vendor-specific schema or processor extensions; if used, document portability impact and provide fallback behavior.

#### 7.2 Data formats

7.2.1. **SHOULD** use ISO 8601 formats for date/time values; must define timezone handling explicitly.

7.2.2. **SHOULD** use BCP 47 (RFC 5646) language codes for internationalization.

### 8. Error handling and resilience

8.1.1. **MUST** fail closed (reject) on non-well-formed or schema-invalid input unless explicit compatibility mode is documented.

> **Rationale**: "Best-effort" parsing can mask malicious payloads or data corruption, leading to security bypasses.

8.1.2. **MUST** provide actionable error messages: location (line/column), rule violated (schema constraint or Schematron assertion ID), and remediation guidance.

8.1.3. **MUST** define and test partial-processing rules if partial acceptance is required; ensure security controls apply to accepted fragments.

### 9. Testing standards

9.1.1. **MUST** maintain test suites for every schema/contract: valid fixtures and invalid fixtures (at least one per major constraint class).

9.1.2. **MUST** include golden input/output fixtures for transformations with deterministic comparison (canonicalized if needed).

9.1.3. **SHOULD** include boundary tests (depth, size) and fuzz inputs in CI for exposed parsers.

> **Rationale**: XML parsing is a common attack surface for DoS and crash vulnerabilities; fuzzing detects edge cases.

### 10. Documentation standards

#### 10.1 Format specification

10.1.1. **MUST** provide documentation for each XML vocabulary describing: purpose, namespaces, versioning policy, required/optional fields, validation method, and compatibility rules.

> **Rationale**: XML contracts persist longer than original authors; undocumented semantics cause integration drift and costly misinterpretations.

10.1.2. **SHOULD** include minimal valid examples and at least one realistic, complex example demonstrating edge cases.

10.1.3. **MUST** record breaking changes in a changelog with migration guidance and deprecation timelines.

#### 10.2 Schema documentation

10.2.1. **MUST** annotate public schema components with `xs:annotation/xs:documentation` describing business semantics, not just structural definitions.

10.2.2. **SHOULD** use `xs:appinfo` for machine-readable metadata (code generation hints, database mappings) separate from human documentation.

### 11. Tooling and automation

#### 11.1 Automated checks

11.1.1. **MUST** implement automated CI/pre-commit checks for: well-formedness, formatting compliance, and schema validation (XSD/Schematron as applicable).

> **Rationale**: Prevents non-compliant XML from entering mainline branches, reducing technical debt and security exposure.

11.1.2. **MUST** treat formatter output as authoritative; reviewers **MUST NOT** request manual formatting changes contradicting automated tooling.

11.1.3. **MUST** pin tool versions (container image tags, lockfiles, or CI action versions) to ensure reproducible validation.

#### 11.2 Editor configuration

11.2.1. **SHOULD** provide `.editorconfig` and/or IDE-specific settings to enforce consistent encoding, line endings, and indentation.

### 12. Accessibility and multi-platform responsiveness

<details>
<summary>Expand: Apply this section only if XML renders as UI (e.g., XHTML, SVG, XForms, Android XML layouts, or XML generating HTML).</summary>

#### 12.1 Accessibility baseline

12.1.1. **MUST** ensure UI XML outputs meet **WCAG 2.2 Level AA** where applicable, including semantic structure, text alternatives, and keyboard operability.

> **Rationale**: Accessibility is a legal and ethical baseline; semantic markup improves assistive technology support.

12.1.2. **MUST** prefer semantic elements over purely presentational wrappers; document any ARIA usage.

#### 12.2 Responsive behavior

12.2.1. **SHOULD** ensure XML-driven UIs support multiple viewport sizes and input methods (mouse/touch/keyboard) when transformed to HTML or native UI.

</details>

### 13. LLM generation and review protocol

#### 13.1 Clarifying questions

13.1.1. **MUST** ask targeted clarifying questions before generating XML if requirements are ambiguous regarding: schema version, namespace policy, security constraints, or consumer platform.

> **Rationale**: Prevents confidently generating incompatible or insecure XML that violates organizational standards.

#### 13.2 Generation behavior

13.2.1. **MUST** provide complete artifacts (not partial snippets) including: XML instance(s), schema(s) if applicable, validation commands, and security notes.

13.2.2. **MUST** include `bash` snippets for validation/running when practical.

#### 13.3 Review behavior

13.3.1. **MUST** produce structured review output containing:
1. **Summary verdict**: Compliant / Partially compliant / Non-compliant
2. **Findings table** with columns: `ID`, `Severity (MUST/SHOULD/MAY)`, `Standard Reference`, `Issue`, `Fix`
3. **Suggested patch** as unified diff when feasible

> **Rationale**: Makes review actionable and consistent across tools and human reviewers.

13.3.2. **MUST** map severity as: **MUST** violations = blocking; **SHOULD** violations = non-blocking (require justification to accept); **MAY** violations = informational.

## Appendices

### Appendix A: Application instructions

**When generating new XML artifacts:**

1. Identify: purpose, consumers/producers, trust boundaries (trusted/untrusted), target schema language (XSD/RELAX NG/Schematron), and performance constraints.
2. Generate:
   - XML instance(s) with proper prolog and encoding
   - Schema(s) and/or Schematron rules (if applicable)
   - Sample validation commands
   - Security configuration notes (XXE protections)
   - Versioning strategy documentation

**When reviewing existing XML artifacts:**

1. Output structured compliance report with:
   - **Critical violations** (MUST standards broken: XXE vulnerabilities, malformed XML, missing encoding, exposed secrets)
   - **Recommendations** (SHOULD standards not met: inconsistent naming, missing annotations, suboptimal parsing strategy)
   - **Passed** (standards met)
2. For each violation, provide:
   - Standard reference (e.g., "5.1.1: XXE Protection")
   - Location (XPath or line number) and problematic code
   - Suggested rewrite using diff syntax
3. Calculate compliance score: `(passed standards / total applicable standards) × 100%`
4. If security violations exist (exposed credentials, XXE risks, unsafe parser configs), prepend a ⚠️ **SECURITY WARNING** banner.

**Response formatting:**
- Bold all **MUST**/**SHOULD**/**MAY** references.
- Use Markdown tables for findings.
- Show before/after diffs for corrections.
- Include validation commands in `bash` code blocks.

### Appendix B: Enforcement checklist

Critical **MUST** items for quick validation:

- [ ] **Well-formedness**: Single root, properly nested tags, quoted attributes (2.1.1)
- [ ] **Encoding**: UTF-8 declared and used (2.1.2)
- [ ] **Formatting**: Automated formatting enforced; no manual style overrides (2.2.1)
- [ ] **Namespaces**: Defined for reusable vocabularies; prefixes stable (3.1.1, 3.1.2)
- [ ] **Validation strategy**: XSD/Schematron defined for interchange formats (4.1.1)
- [ ] **XXE protection**: External entities disabled; DTDs prohibited for untrusted input (5.1.1, 5.1.2)
- [ ] **Resource limits**: Size, depth, expansion limits enforced (5.1.3)
- [ ] **Secrets**: No credentials in XML or comments (2.3.5, 5.3.1)
- [ ] **Fail closed**: Reject invalid input unless documented compatibility mode (8.1.1)
- [ ] **CI automation**: Pre-commit/CI checks for well-formedness, formatting, validation (11.1.1)
- [ ] **Tool pinning**: Reproducible tool versions in CI (11.1.3)
- [ ] **Documentation**: Schema annotations and changelog maintained (10.1.1, 10.1.3)

### Appendix C: Examples

#### C.1 Non-compliant vs. compliant: XXE vulnerability

**Non-compliant (insecure parser configuration):**
```java
// Vulnerable to XXE
DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
DocumentBuilder db = dbf.newDocumentBuilder();
Document doc = db.parse(untrustedInput);  // Dangerous!
```

**Compliant (secure by default):**
```java
// Secure configuration
DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
dbf.setFeature("http://apache.org/xml/features/disallow-doctype-decl", true);
dbf.setFeature("http://xml.org/sax/features/external-general-entities", false);
dbf.setFeature("http://xml.org/sax/features/external-parameter-entities", false);
dbf.setXIncludeAware(false);
dbf.setExpandEntityReferences(false);
DocumentBuilder db = dbf.newDocumentBuilder();
Document doc = db.parse(untrustedInput);
```

#### C.2 Non-compliant vs. compliant: Namespace and versioning

**Non-compliant (missing namespace, inconsistent naming):**
```xml
<?xml version="1.0"?>
<Order>
  <orderID>12345</orderID>
  <customer_name>Acme Corp</customer_name>
  <Items>
    <Item><id>1</id></Item>
  </Items>
</Order>
```

**Compliant (proper namespacing and structure):**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<ord:order xmlns:ord="https://example.com/schemas/order/v1"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="https://example.com/schemas/order/v1 order-v1.xsd"
           ord:version="1.0.0">
  <ord:order-id>12345</ord:order-id>
  <ord:customer-name>Acme Corp</ord:customer-name>
  <ord:items>
    <ord:item ord:id="1"/>
  </ord:items>
</ord:order>
```

#### C.3 Non-compliant vs. compliant: Schema documentation

**Non-compliant (undocumented, weak typing):**
```xml
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema">
  <xs:element name="product">
    <xs:complexType>
      <xs:sequence>
        <xs:element name="price" type="xs:string"/>
        <xs:element name="qty" type="xs:string"/>
      </xs:sequence>
    </xs:complexType>
  </xs:element>
</xs:schema>
```

**Compliant (documented, strongly typed):**
```xml
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
           targetNamespace="https://example.com/schemas/product/v1"
           xmlns:prod="https://example.com/schemas/product/v1">
  <xs:element name="product" type="prod:ProductType">
    <xs:annotation>
      <xs:documentation>
        Represents a sellable product with pricing and inventory data.
        Version: 1.0.0
      </xs:documentation>
    </xs:annotation>
  </xs:element>

  <xs:complexType name="ProductType">
    <xs:sequence>
      <xs:element name="price" type="xs:decimal">
        <xs:annotation>
          <xs:documentation>Unit price in USD. Must be non-negative.</xs:documentation>
        </xs:annotation>
      </xs:element>
      <xs:element name="qty" type="xs:nonNegativeInteger">
        <xs:annotation>
          <xs:documentation>Available inventory quantity.</xs:documentation>
        </xs:annotation>
      </xs:element>
    </xs:sequence>
  </xs:complexType>
</xs:schema>
```
