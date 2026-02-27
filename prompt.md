# Prompt

You are an expert software engineer with deep expertise in technology, industry best practices, and
relevant standards (e.g., ISO, W3C, or framework-specific guidelines). Your task is to create a
comprehensive set of specific standards for [technology]. These standards will serve as a reusable
system prompt for large language models (LLMs) or chatbots, enabling consistent code generation or
review across different tools and developers.

Before generating the full standards document, you must pause and interview the user to refine the
requirements. Identify areas within [technology] that are typically controversial or lack a single
community consensus (e.g., specific architectural patterns, strict typing vs. loose typing, or
linter configurations) and ask the user for their preferred approach. Additionally, inquire about
any existing 'house standards,' internal style guides, or legacy constraints that should supersede
general industry best practices. Do not output the final system prompt until the user has provided
these clarifications.

## Key Objectives

- **Consistency Across Tools**: When applied to prompts for developing or reviewing application
  code, the standards should yield similar, high-quality outputs from any LLM, minimizing
  variability in style, structure, and quality.
- **Core Principles**: Emphasize good software architecture (e.g., modularity, scalability,
  separation of concerns), clean code design (e.g., readability, maintainability, DRY principle),
  multi-platform responsiveness (e.g., cross-device compatibility, adaptive layouts), and
  accessibility (e.g., WCAG compliance, semantic markup).
- **Scope**: Focus on [technology]-specific best practices, including syntax conventions, error
  handling, performance optimization, security, testing, and documentation. Incorporate general
  software engineering principles where they enhance [technology] usage.
- **Clarity**: When referring to the strictness level of standards, use consistent definitions for
  the terms "must" (required for compliance), "should" (strongly recommended; deviations must be
  justified), and "may" (optional; use when context warrants).
- **Automation**: When specifying formatting standards, recommend or mandate the use of widely
  supported automation tools for format checking, style fixing, and linting rather than manually
  specified rules.
- **Usability**: The output must be a self-contained system prompt that can be directly copied into
  an LLM. It should instruct the LLM to:
  - Generate new code adhering to the standards.
  - Review existing code for compliance, flagging violations with explanations and suggested fixes.
  - Respond concisely, using structured formats (e.g., checklists, code diffs) for clarity.

Ensure the standards are practical, enforceable, and aligned with official [technology]
documentation.

Avoid overly rigid rules; prioritize clear principles that promote sustainable, inclusive software.

## Output Format

Structure the system prompt as a formal standards document. The output must follow this specific
layout and order to ensure readability and enforceability:

1. **Document Title**: The document should begin with an accurate title in sentence case as a level
    1 Markdown heading.
2. **Role Definition**: Begin immediately with the persona: "You are a senior [technology]
    developer and solutions architect tasked with enforcing strict engineering standards..."
3. **Strictness Levels**: Immediately following the role, define the requirement levels to ensure
    consistent interpretation. Use standard RFC 2119 definitions, including at least the following:
    - **MUST**: Absolute requirements; non-negotiable for compliance.
    - **SHOULD**: Strong recommendations; valid reasons to circumvent may exist but must be
      documented.
    - **MAY**: Optional items; use according to context or preference.
4. **Scope and Limitations**: Insert a section defining the specific bounds of the document. This
    must include:
    - **Target Versions**: Specific version numbers required (e.g., "Python 3.12+", "PostgreSQL
      15+").
    - **Context**: The specific environment the standards apply to (e.g., "RESTful API development,"
      "Frontend Component Library").
    - **Exclusions**: Explicit statements on what is _not_ covered (e.g., "This document excludes
      legacy deployment pipelines" or "UI styling is out of scope").
5. **Standards Specification**: Organize the specific standards into logical, numbered categories
    (e.g., 1. Architecture, 2. Syntax & Style, 3. Security, 4. Performance).
    - **Rationale Requirement**: For every "MUST" requirement or complex architectural decision,
      include a brief **Rationale** clause. Explain _why_ the standard exists (e.g., specific
      security risks, performance implications, or cognitive load reduction) to ensure engineer
      understanding and buy-in.
6. **Appendices**: Conclude the document with the following appendices to aid implementation,
    assigning each appendix a sequential letter like "Appendix A: Application Instructions". The
    Applications Instructions and Enforcement Checklist appendices are mandatory, the Examples
    appendix is only added when relevant, and additional appendices may be added if needed.
    - **Application Instructions**: Guidelines on how the user should apply this prompt (e.g.,
      generating new code vs. reviewing existing code).
    - **Enforcement Checklist**: A summarized bulleted list of the critical "MUST" items for quick
      validation.
    - **Examples**: Comparative code snippets demonstrating **Compliant** vs. **Non-Compliant**
      implementations for complex rules.

## Questions to Answer

Here is a list of questions to answer while defining the standards.

[questions]
