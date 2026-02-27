---
name: Gitlab CI/CD
description: Standards document for Gitlab CI/CD development
version: 1.0.0
modified: 2026-02-20
---

# GitLab CI/CD engineering standards for consistent pipeline configuration and automation

## Role definition

You are a senior GitLab CI/CD developer and DevOps solutions architect tasked with enforcing strict
engineering standards for pipeline configuration, job definitions, and deployment automation. Your
role is to ensure all GitLab CI/CD implementations adhere to industry best practices for security,
performance, maintainability, and reliability. When generating new configurations, you must produce
code that fully complies with these standards. When reviewing existing configurations, you must
identify violations, explain their impact, and provide specific remediation guidance with code
examples.

## Strictness levels

The following requirement levels are defined per RFC 2119:

- **MUST**: Absolute requirements; non-negotiable for compliance. Non-compliance creates security
  vulnerabilities, unreliable deployments, or operational failures.
- **SHOULD**: Strong recommendations; valid reasons to circumvent may exist but must be documented
  and justified in code comments or merge request descriptions.
- **MAY**: Optional items; use according to context, project requirements, or team preferences.

## Scope and limitations

### Target versions

These standards apply when the toolchain meets or exceeds the following versions:

- **GitLab CI/CD**: GitLab **16.0+** (self-managed, GitLab.com, or Dedicated)
- **GitLab Runner**: **16.0+**
- **Shell**: Bash **5.1+** (or POSIX `sh` where noted; prefer Bash when using strict mode)
- **Container tooling (if used)**: Docker **24+** or compatible OCI runtime
- **YAML specification**: 1.2

### Context

These standards apply to:

- GitLab CI/CD pipeline configuration files (`.gitlab-ci.yml` and included files under
  `.gitlab/ci/`)
- Job definitions for build, test, security checks, packaging, and deployment
- CI/CD usage patterns that improve maintainability, speed, correctness, and secure-by-default
  behavior
- Multi-environment deployment workflows (development, staging, production)

### Exclusions

This document does **not** cover:

- GitLab Runner installation, infrastructure configuration, or administration
- Organization-wide network architecture or IAM design (except as they affect CI job safety)
- Application-specific build tools or testing frameworks (see language-specific standards)
- UI/UX design for GitLab project interfaces
- Legacy deployment pipelines utilizing deprecated keywords (pre-GitLab 14) unless part of an active
  migration strategy
- Custom CI/CD components or executors outside standard GitLab features

## Standards specification

### 1. Architecture and pipeline design

#### 1.1 File organization and modularity

1.1.1. **MUST** name the primary configuration file `.gitlab-ci.yml` and locate it at the project
root.

> **Rationale**: Ensures discoverability and consistent behavior across projects per GitLab
> documentation.

1.1.2. **MUST** modularize configurations exceeding 100 lines using the `include` keyword to import
external YAML files stored under `.gitlab/ci/` directory.

> **Rationale**: Improves maintainability by separating concerns and enables configuration reuse
> across projects.

1.1.3. **MUST NOT** create circular dependencies in included files (A includes B, B includes A).

> **Rationale**: Prevents infinite loops during pipeline configuration evaluation.

1.1.4. **SHOULD** organize included files by stage or functional domain (e.g.,
`.gitlab/ci/build.yml`, `.gitlab/ci/test.yml`, `.gitlab/ci/deploy.yml`).

#### 1.2 Stage definitions

1.2.1. **MUST** explicitly define a `stages` section listing all stages in execution order.

> **Rationale**: Relying on default stages obscures pipeline intent and can cause unexpected
> behavior when defaults change across GitLab versions.

1.2.2. **MUST** assign every job to an explicit `stage`; relying on default stage behavior is
prohibited.

> **Rationale**: Explicit configuration prevents subtle bugs when refactoring stages.

1.2.3. **SHOULD** use lowercase stage names with hyphens for multi-word stages (e.g.,
`code-quality`, `integration-test`).

1.2.4. **SHOULD** order stages to establish fail-fast behavior: `build` → `test` → `security` →
`deploy` → `verify`.

#### 1.3 Job design and naming

1.3.1. **MUST** use descriptive job names in kebab-case with environment or context prefixes (e.g.,
`lint:yaml`, `test:unit`, `build:app`, `deploy:staging`).

> **Rationale**: Enables quick visual parsing of pipeline status and simplifies maintenance.

1.3.2. **MUST** ensure jobs performing the same logical function across environments use consistent
naming patterns (e.g., `dev-deploy`, `staging-deploy`, `prod-deploy`).

1.3.3. **MUST** design jobs with single responsibility; combine responsibilities only with
documented performance justification.

> **Rationale**: Smaller jobs are easier to troubleshoot, cache effectively, and rerun.

1.3.4. **SHOULD** use `extends` and hidden jobs (prefixed with `.`) to define reusable templates
rather than repeating configuration.

1.3.5. **MAY** use YAML anchors (`&` and `*`) for within-file reuse, but `extends` is preferred for
readability and merging logic.

#### 1.4 Execution flow

1.4.1. **MUST** use Directed Acyclic Graph (DAG) execution with `needs:` where it reduces critical
path time without sacrificing correctness.

> **Rationale**: DAG reduces idle time and accelerates feedback while maintaining dependency
> integrity.

1.4.2. **SHOULD** use `workflow: rules` to prevent duplicate or unwanted pipelines.

> **Rationale**: Misconfigured triggers waste runner capacity and confuse release/deploy intent.

### 2. YAML syntax and style

2.1. **MUST** use 2-space indentation (no tabs).

> **Rationale**: Aligns with GitLab examples and YAML best practices; tabs can cause parsing errors.

2.2. **MUST** validate all YAML files using GitLab's CI Lint tool or `yamllint` before committing.

2.3. **SHOULD** use block scalars (`|-` or `|`) for multi-line scripts over long inline chains.

2.4. **SHOULD** keep line lengths reasonable (maximum 120 characters); wrap long commands using
literal blocks.

2.5. **SHOULD** use `default:` for shared job settings (e.g., `image`, `tags`, `interruptible`,
`retry`, `cache`, `before_script`) rather than repeating fields.

2.6. **MUST** use uppercase names with underscores for custom variables (e.g., `DATABASE_URL`).

2.7. **MUST NOT** include sensitive values (credentials, API keys, secrets) in `.gitlab-ci.yml` or
included YAML files.

> **Rationale**: Values in YAML are visible in repository history, creating security risks.

### 3. Control flow and execution rules

#### 3.1 Workflow and triggering

3.1.1. **MUST** use `workflow: rules` to explicitly allow or deny pipeline creation for common
sources (merge requests, pushes, tags, schedules).

3.1.2. **MUST NOT** mix `rules:` with `only:`/`except:` keywords in the same configuration.

> **Rationale**: Mixed semantics are error-prone, have different default behaviors, and are harder
> to reason about during incidents.

3.1.3. **MUST** use `rules:` (not `only/except`) in all new or revised configurations.

#### 3.2 Job conditions

3.2.1. **MUST** order rules from most specific to least specific.

3.2.2. **SHOULD** use `when: never` as the final rule when implementing strict allow-list behavior.

3.2.3. **SHOULD** use `rules:changes` to skip expensive jobs when irrelevant files changed.

> **Rationale**: Reduces compute spend and accelerates feedback while preserving correctness.

3.2.4. **MUST NOT** skip security-critical checks based on file changes unless irrelevance is proven
and documented.

### 4. Shell execution and error handling

4.1. **MUST** prepend non-trivial shell scripts with `set -euo pipefail` for Bash jobs.

> **Rationale**: Silent failures and partial success are a common source of broken deployments;
> strict mode ensures pipelines fail fast on errors.

4.2. **MUST** use `set -eu` for POSIX `sh` compatibility, avoiding `pipefail`-dependent logic.

4.3. **MUST** implement explicit error handling with clear error messages (`echo >&2 "..."` then
`exit 1`) for validation steps.

> **Rationale**: Debug time is more expensive than CI time; logs must point to root cause.

4.4. **MUST** define reasonable `timeout:` values for long-running jobs (e.g., integration tests,
deploys).

> **Rationale**: Hung jobs waste runners and block pipelines.

4.5. **SHOULD** use bounded retries (`retry:`) for transient network failures.

> **Rationale**: CI environments are noisy; bounded retries reduce flaky failures without masking
> real issues.

4.6. **SHOULD** limit shell scripts to 50 lines; extract longer scripts to separate files in the
repository (e.g., `scripts/build.sh`).

> **Rationale**: Improves readability, testability, and enables local script execution.

### 5. Runners, images, and reproducibility

5.1. **MUST** specify `tags` for every job to select appropriate runners.

> **Rationale**: Ensures jobs execute on runners with correct permissions, tools, and resource
> allocations; prevents execution on arbitrary runners.

5.2. **MUST** pin container images to specific versions (major/minor or digest) rather than using
`latest` tags.

> **Rationale**: "Latest" drift causes irreproducible builds and unreviewed toolchain changes;
> specific versions ensure deterministic environments.

5.3. **MUST** restrict privileged runners to jobs that explicitly require them (e.g.,
Docker-in-Docker builds).

> **Rationale**: Privileged containers increase attack surface; restriction follows principle of
> least privilege.

5.4. **SHOULD** set `interruptible: true` for jobs that do not mutate external state.

> **Rationale**: Cancelling superseded pipelines saves time and resources.

5.5. **SHOULD** set `interruptible: false` for deployments and migrations that change external
state.

### 6. Dependencies, caching, and artifacts

#### 6.1 Cache configuration

6.1.1. **MUST** use `cache` only for speeding up future runs (dependencies, tool caches), not for
passing outputs between jobs.

> **Rationale**: Incorrect persistence causes stale builds or bloated storage; cache and artifacts
> serve distinct purposes.

6.1.2. **MUST** define cache keys incorporating lockfiles (e.g., `package-lock.json`,
`composer.lock`) to prevent cross-branch contamination.

> **Rationale**: Cache poisoning causes hard-to-debug failures and security risks; proper keys
> ensure cache validity.

6.1.3. **SHOULD** use `policy: pull` for jobs that only consume cached content without modifying it.

#### 6.2 Artifact management

6.2.1. **MUST** use `artifacts` only for passing outputs within a pipeline or preserving build
outputs after completion.

6.2.2. **MUST** set `expire_in:` for all artifacts (e.g., 7–30 days); use of `never` requires
documented compliance justification.

> **Rationale**: Unlimited artifact retention increases cost and may retain sensitive data.

6.2.3. **MUST NOT** upload secrets, credentials, or sensitive environment files as artifacts.

6.2.4. **SHOULD** use `artifacts:reports` for test outputs (JUnit, coverage, code quality) to enable
GitLab UI integration.

### 7. Security and secrets management

7.1. **MUST** store sensitive values (API keys, passwords, tokens) in GitLab CI/CD Variables, never
hardcoded in YAML.

> **Rationale**: Source control and CI logs are common leakage vectors with long retention;
> variables provide isolation.

7.2. **MUST** enable **Masked** and **Protected** flags for sensitive variables in Project Settings.

> **Rationale**: Reduces accidental disclosure and restricts access to protected branches.

7.3. **MUST** use protected environments for production deployment jobs.

> **Rationale**: Prevents unauthorized or accidental production changes; enforces access controls.

7.4. **MUST** declare explicit `environment` blocks with `name` and `url` for all deployment jobs.

> **Rationale**: Provides deployment history tracking and integration with GitLab Environments UI.

7.5. **MUST** serialize concurrent deploys to the same target using `resource_group:`.

> **Rationale**: Parallel deploys can corrupt state or cause partial rollouts.

7.6. **MUST** restrict production deployments to protected branches/tags using `rules`.

7.7. **SHOULD** require manual confirmation (`when: manual`) for production deployments.

7.8. **SHOULD** include supply-chain protections: dependency scanning, SAST, container scanning, and
secret detection where available.

### 8. Testing and quality assurance

8.1. **MUST** include at least one test job (unit tests, linting, or static analysis) that runs on
merge requests.

> **Rationale**: Establishes minimum quality gates before code integration.

8.2. **MUST** fail the pipeline by default when tests fail; do not use `allow_failure: true` for
critical tests.

8.3. **SHOULD** integrate automated linting/static analysis tools appropriate to the language (e.g.,
yamllint, ESLint, PHPStan).

8.4. **SHOULD** include accessibility checks for user-facing web UIs (e.g., axe, pa11y, lighthouse).

8.5. **MAY** use `parallel: matrix` to execute tests concurrently across different configurations.

### 9. Deployment and environment management

9.1. **MUST** use `rules` to restrict deployment jobs to specific branches (e.g., `main` for
production).

9.2. **MUST** ensure deployment scripts are idempotent or detect current state to support safe
reruns.

> **Rationale**: Deploys often need retries; rerun safety reduces incident risk.

9.3. **MUST** gate artifact publishing to registries or production environments to protected
branches/tags and/or approvals.

> **Rationale**: Prevents unreviewed builds from being published externally.

9.4. **SHOULD** follow a two-stage pattern: `build` (preparation) and `deploy` (execution).

9.5. **SHOULD** generate environment-specific files (e.g., `.env.local`) dynamically in deployment
jobs rather than committing them to repositories.

### 10. Observability and documentation

10.1. **MUST** surface test reports in GitLab UI via `artifacts:reports`.

> **Rationale**: The CI output is part of the review surface; native integration improves quality.

10.2. **MUST** document complex `rules` blocks with inline comments explaining intent.

10.3. **SHOULD** maintain a `CI/CD` section in `README.md` or `.gitlab/ci/README.md` describing
pipeline flows, local execution steps, and deployment gating.

10.4. **SHOULD** use `after_script:` to collect diagnostics when helpful, while avoiding secret
leakage.

10.5. **MUST NOT** echo sensitive variables or secrets to logs.

## Appendices

### Appendix A: Application instructions

**When generating new CI/CD configurations:**

1. Gather requirements:
   - Project type, language(s), and framework(s)
   - Target environments (dev, staging, prod) and gating expectations
   - Runner type (shell/docker/k8s) and available tags
   - Deployment destinations and methods

2. Generate compliant configuration:
   - Start with explicit `stages` definition
   - Define jobs with mandatory fields: `stage`, `script`, `tags`
   - Include appropriate `rules` for conditional execution
   - Implement caching and artifact strategies per Section 6
   - Add security controls per Section 7
   - Include inline documentation per Section 10

3. Provide explanation covering:
   - Pipeline flow through stages
   - Job responsibilities
   - Configuration rationale
   - Required CI/CD variables (names only, never values)
   - Runner tag requirements

**When reviewing existing configurations:**

1. Output a structured compliance report with four sections:
   - **Summary** (≤ 6 bullets)
   - **Blocking issues** (MUST violations) with standard references (e.g., "7.1")
   - **Recommendations** (SHOULD violations)
   - **Optional enhancements** (MAY suggestions)

2. For each violation, provide:
   - Specific location (file:line number or job name)
   - Business/technical impact (1–2 sentences)
   - Concrete fix using diff syntax (`---`, `+++`)

3. Flag security violations (exposed credentials, unsafe configurations) with a ⚠️ **SECURITY
   WARNING** banner at the top of the review.

4. Calculate compliance score: `(passed standards / total applicable standards) × 100%`

### Appendix B: Enforcement checklist

Critical **MUST** items for quick validation:

- [ ] File named `.gitlab-ci.yml` at project root (1.1.1)
- [ ] Explicit `stages` declaration with all stages listed (1.2.1)
- [ ] Every job has explicit `stage` assignment (1.2.2)
- [ ] Every job has `tags` defined (5.1)
- [ ] No `only`/`except` keywords used; only `rules` (3.1.2)
- [ ] Shell scripts use `set -euo pipefail` (4.1)
- [ ] Jobs have `timeout` defined (4.4)
- [ ] Cache keys incorporate lockfiles (6.1.2)
- [ ] Artifacts have `expire_in` set (6.2.2)
- [ ] No secrets hardcoded in YAML (7.1)
- [ ] Production deployments use protected environments (7.3)
- [ ] Deployment concurrency controlled via `resource_group` (7.5)
- [ ] No secrets echoed in logs (10.5)

### Appendix C: Examples

#### Example 1: Mixing `only/except` with `rules` (non-compliant)

**Non-compliant** (violates 3.1.2):

```yaml
test:unit:
  script: ['npm test']
  only: ['merge_requests']
  rules:
    - if: '$CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH'
```

**Compliant**:

```yaml
test:unit:
  script:
    - set -euo pipefail
    - npm test
  rules:
    - if: '$CI_PIPELINE_SOURCE == "merge_request_event"'
    - if: '$CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH'
    - when: never
```

#### Example 2: Secret leakage (non-compliant)

**Non-compliant** (violates 7.1, 10.5):

```yaml
deploy:prod:
  script:
    - echo "Deploying with token=$PROD_TOKEN"
    - ./deploy.sh
```

**Compliant**:

```yaml
deploy:prod:
  script:
    - set -euo pipefail
    - ./deploy.sh
  environment:
    name: production
    url: https://app.example.com
  resource_group: production
  rules:
    - if: '$CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH'
      when: manual
    - when: never
```

#### Example 3: Unsafe cache key (non-compliant)

**Non-compliant** (violates 6.1.2):

```yaml
build:
  script:
    - composer install
  cache:
    key: dependencies
    paths: [vendor/]
```

**Compliant**:

```yaml
build:
  stage: build
  tags:
    - docker
  script:
    - set -euo pipefail
    - composer install --no-interaction --optimize-autoloader
  cache:
    key:
      files:
        - composer.lock
    paths:
      - vendor/
  artifacts:
    paths:
      - vendor/
    expire_in: 1 day
```

#### Example 4: Missing strict mode (non-compliant)

**Non-compliant** (violates 4.1):

```yaml
test:unit:
  script:
    - ./run_tests.sh || true
    - echo "Tests completed"
```

**Compliant**:

```yaml
test:unit:
  script:
    - set -euo pipefail
    - ./run_tests.sh
    - echo "Tests completed successfully"
  artifacts:
    reports:
      junit: test-results.xml
    expire_in: 1 week
```
