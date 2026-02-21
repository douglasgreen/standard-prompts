# AI-Assisted Software Development Guide

This guide is designed for human software engineering teams to effectively leverage Large Language Models (LLMs) and chatbots throughout the 7-stage Software Development Life Cycle (SDLC). By combining your team's domain knowledge with the provided strict engineering standards, you can prompt AI assistants to generate secure, scalable, and highly consistent code, architecture, and documentation.

## Core Prompting Rules (The `chatbot.md` Standard)

Before starting the SDLC, establish your AI workspace according to the **Chatbot** standards:

1. **Create Context Files**: Initialize your repository with a `CLAUDE.md`, `.cursorrules`, or `AGENTS.md` file. Use this to define your tech stack and strictly reference the standards below (e.g., "Always adhere to `javascript.md` and `error-handling.md`").
2. **Use Structural Delimiters**: When prompting, use XML tags to separate concerns (e.g., `<role>`, `<context>`, `<task>`, `<constraints>`).
3. **Data Sanitization**: **Never** paste real API keys, PII, or proprietary trade secrets into the chatbot. Use placeholders like `<YOUR_API_KEY>` (per RFC 2606).

Here is the master formula to use when writing prompts at any stage:
```xml
<role>You are a senior developer and solutions architect.</role>
<context>We are in the phase for a.</context>
<standards>
Follow the strict requirements defined in the following provided standards:
-
-
</standards>
<task>Your specific request goes here.</task>
```

---

## Stage 1: Planning
**Goal**: Define scope, feasibility, initial risks, and project plan.

During planning, the chatbot excels at helping you structure thoughts, identify early risks, and set up documentation foundations.

**Applicable Standards**:
*   `general/project-docs.md`
*   `general/security-and-privacy.md`

**How to prompt the chatbot**:
*   **Drafting the README**: Provide the bot with a brief project summary and feed it `project-docs.md`. Instruct it to generate a standard repository `README.md` following the Diátaxis framework (Explanation, How-to, etc.).
*   **Initial Threat Modeling**: Supply `security-and-privacy.md` and ask the bot to act as a Security Architect. Prompt: *"Review our high-level project plan and generate a threat model identifying trust boundaries, assets, and initial OWASP Top 10 mitigation strategies."*

---

## Stage 2: Requirements Gathering and Analysis
**Goal**: Document detailed functional and non-functional requirements (SRS).

AI can help translate messy stakeholder meeting notes into highly structured, testable, and unambiguous requirements.

**Applicable Standards**:
*   `general/dev-spec.md`
*   `general/technical-writing.md`

**How to prompt the chatbot**:
*   **Generate Specifications**: Feed the bot your raw notes along with `dev-spec.md`. 
*   **Prompt Example**: 
    ```xml
    <instruction>
    Act as a senior software specification developer. Translate the following meeting notes into a formal Developer Specification.
    Ensure all functional requirements use the EARS syntax (WHEN... THEN) or Gherkin, are strictly atomic, and have unique IDs (e.g., `REQ-AUTH-001`). Ensure the document includes all 9 sections mandated by dev-spec.md.
    </instruction>
    ```
*   **Reviewing Specs**: Ask the bot to evaluate your existing requirements against the `dev-spec.md` "Testability Standards" to flag any vague adjectives (like "fast" or "user-friendly") and suggest quantitative metrics.

---

## Stage 3: Design
**Goal**: Create Software Design Documents (SDD), Architecture Decision Records (ADRs), UI/UX flows, and Database Schemas.

Use the chatbot to generate visualizations, define data structures, and validate architectural patterns.

**Applicable Standards**:
*   `general/architecture.md` & `general/design-pattern.md`
*   `sql/schema.md`
*   `general/ui-ux.md` & `general/accessibility.md`
*   `other/mermaid.md`
*   `general/cryptography.md`

**How to prompt the chatbot**:
*   **Architecture & Patterns**: Supply `architecture.md` and `design-pattern.md`. Prompt the bot to propose an architectural style (e.g., Event-Driven, Layered) and write formal ADRs for your tech stack choices.
*   **Database Schema Design**: Feed the bot your data requirements and `sql/schema.md`. Prompt: *"Generate the DDL for PostgreSQL 15+. Ensure tables are in 3NF, use `snake_case`, apply UUID/BIGINT primary keys, and include `created_at`/`updated_at` audit fields. Avoid `FLOAT` for currency."*
*   **Diagramming**: Supply `mermaid.md`. Ask the bot to generate C4 context, container, or sequence diagrams. **Crucial constraint**: Remind the bot to include `accTitle` and `accDescr` for accessibility, and to use semantic shapes and colors.
*   **UI/UX Design**: Supply `ui-ux.md` and `accessibility.md`. Ask the bot to define the semantic HTML structure and ARIA landmark strategy for your proposed UI views.

---

## Stage 4: Development (Implementation / Coding)
**Goal**: Write the actual software code, integrating modules and APIs.

This is the most standard-intensive phase. You must narrow the bot's focus by feeding it the specific standards for your chosen stack alongside cross-cutting standards.

**Applicable Guidelines (Use these FIRST to bootstrap the project)**:
*   `guidelines/java-project-setup.md`
*   `guidelines/php-project-setup.md`
*   `guidelines/python-project-setup.md`

**Applicable Language/Framework Standards**:
*   **JavaScript/TypeScript**: `javascript.md`, `nodejs.md`, `vuejs.md`
*   **PHP**: `php.md`, `symfony.md`, `phpdoc.md`, `cookie.md`, `session.md`
*   **Python**: `python.md`
*   **Java**: `java.md`
*   **Go**: `go.md`
*   **Frontend**: `html-css.md`, `bootstrap.md`, `twig.md`

**Applicable Cross-Cutting Standards**:
*   `general/error-handling.md`, `general/naming-glossary.md`, `general/datetime-handling.md`, `general/i18n-and-l10n.md`, `sql/query.md`

**How to prompt the chatbot**:
*   **Bootstrapping**: *"Using `python-project-setup.md`, give me the bash commands and configuration files (`pyproject.toml`, `Makefile`) to initialize our new microservice."*
*   **Feature Generation**: 
    ```xml
    <role>Senior Node.js Developer</role>
    <standards>Follow nodejs.md, javascript.md, error-handling.md, and naming-glossary.md strictly.</standards>
    <task>Implement the user creation service layer. Use dependency injection, async/await, and validate inputs at the boundary using Zod. Use custom error classes for failures.</task>
    ```
*   **SQL Queries**: Supply `sql/query.md`. Require the bot to use parameterized queries (to prevent SQL injection) and SARGable `WHERE` clauses.
*   **Code Review**: Paste a snippet of human-written code into the bot and prompt: *"Review this code against our `go.md` and `error-handling.md` standards. Output a structured compliance report with a findings table and a unified diff patch for any MUST violations."*

---

## Stage 5: Testing
**Goal**: Verify the software works via unit, integration, and user acceptance testing.

The chatbot can generate agile test specifications for your QA team, as well as the actual automated test code for your CI pipeline.

**Applicable Standards**:
*   `general/test-spec.md` (For manual/agile QA documentation)
*   `javascript/vitest.md` (For JS/TS unit/component testing)
*   `javascript/playwright.md` (For E2E UI testing)
*   `php/phpunit.md` (For PHP testing)

**How to prompt the chatbot**:
*   **Automated Tests**: Feed the bot your source code and the relevant framework standard (e.g., `vitest.md`). Prompt: *"Generate unit tests for this module using the Arrange-Act-Assert (AAA) pattern. Use deterministic mocks for external network calls and include boundary value tests."*
*   **Playwright E2E Tests**: Supply `playwright.md`. Prompt: *"Write a Playwright test for the checkout flow. Use web-first assertions (`toBeVisible()`), prioritize `getByRole` locators over CSS selectors, and ensure tests run in isolation without shared mutable state."*
*   **Manual Test Specs**: Supply `test-spec.md`. Instruct the bot to write a UI-focused manual test plan. Ensure it does *not* include database queries, as manual testers should verify states via the UI only.

---

## Stage 6: Deployment
**Goal**: Release the software safely to production environments.

Use the chatbot to generate safe, reproducible infrastructure and CI/CD pipelines.

**Applicable Standards**:
*   `other/gitlab-ci-cd.md`
*   `other/bash-scripting.md`
*   `general/cli.md` (If building operational CLI tools)

**How to prompt the chatbot**:
*   **CI/CD Pipeline**: Supply `gitlab-ci-cd.md`. Prompt: *"Generate a `.gitlab-ci.yml` file for our Node.js app. It must define explicit stages (build, test, security, deploy), use `rules:` instead of `only/except`, pin all container images to explicit versions, and enforce `set -euo pipefail` in all shell scripts."*
*   **Deployment Scripts**: Supply `bash-scripting.md`. Prompt the bot to write deployment bash scripts. Explicitly instruct it to use `#!/usr/bin/env bash`, trap exits for cleanup, and use arrays for command arguments to prevent word-splitting vulnerabilities.
*   **Secrets Management**: Remind the bot via `security-and-privacy.md` that all deployment scripts must rely on environment variables injected by the CI runner, with zero hardcoded credentials.

---

## Stage 7: Maintenance and Support
**Goal**: Monitor, patch, upgrade, and maintain the software.

When upgrading frameworks or tracking down bugs, the AI can drastically reduce the time spent deciphering changelogs and refactoring legacy code.

**Applicable Standards**:
*   `general/upgrade.md`
*   `general/project-docs.md`

**How to prompt the chatbot**:
*   **Version Upgrades**: When moving between major versions (e.g., PHP 8.3 to 8.4, or Vue 3.4 to 3.5), supply `upgrade.md`. Prompt: *"Act as a Senior Software Upgrade Developer. We are upgrading our Symfony app. Create a batch upgrade plan that upgrades leaf packages first. Provide the commands to run automated refactoring tools (like Rector) in atomic, single-rule commits."*
*   **Refactoring Tech Debt**: Paste a legacy module and ask the bot to bring it up to current standards (e.g., removing `@` suppressions in PHP, migrating `var` to `const/let` in JS).
*   **Updating Documentation**: Feed the bot `project-docs.md` and the new code. Ask it to update the existing `README.md` and `CHANGELOG.md` using Semantic Versioning guidelines and active voice. Ensure it maintains the Diátaxis framework structure.
