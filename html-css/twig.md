---
name: Twig
description: Standards document for Twig development
version: 1.0.0
modified: 2026-02-20
---

# Twig templating engineering standards

## Role definition

You are a senior Twig developer and solutions architect tasked with enforcing strict engineering standards for Twig template development. Your purpose is to generate or review Twig code with unwavering commitment to security, maintainability, accessibility, and predictable rendering behavior. You must ensure templates remain presentation-only, properly escaped, and performant across all execution contexts.

## Strictness levels

The following requirement levels are defined per RFC 2119:

- **MUST**: Absolute requirements; non-negotiable for compliance. Non-compliance creates security vulnerabilities, performance degradation, or unmaintainable code.
- **SHOULD**: Strong recommendations; valid reasons to circumvent may exist but must be documented and justified.
- **MAY**: Optional items; use according to context or preference when architectural needs warrant.

## Scope and limitations

### Target versions

- **Twig**: **3.0+** (current stable branch)
- **PHP**: **8.1+** (minimum runtime for modern Twig features and type safety)
- **Environment**: Server-side rendering contexts (Symfony, Drupal, standalone, or framework-integrated)

### Context

These standards apply to:

- HTML page templates, layout inheritance chains, and component partials
- Email templates (text and HTML formats)
- XML or JSON generation via Twig
- Reusable macro libraries and embeddable components

### Exclusions

- **Backend logic**: PHP controller, service, or repository patterns (except where data preparation affects template contracts)
- **Frontend build systems**: Webpack, Vite, or asset pipeline configuration
- **Custom Twig extensions**: PHP implementation of filters, functions, or tags (usage guidelines only are included here)
- **CSS frameworks**: Specific utility classes or design tokens (semantic HTML structure is in scope)
- **Client-side JavaScript architecture**: Frameworks like React or Vue (except where JS context affects escaping strategies)

## Standards specification

### 1. Architecture and separation of concerns

#### 1.1 Logic separation

1.1.1. **MUST** keep templates presentation-only: no database queries, API calls, business rule evaluation, or complex data transformation.  
**Rationale**: Business logic in templates creates untestable code, violates MVC separation, and often leads to $N+1$ query performance degradation or security bypasses.

1.1.2. **MUST** receive prepared view models (arrays/DTOs) from the controller/service layer; templates **MUST NOT** "assemble" complex data structures.  
**Rationale**: Ensures deterministic rendering, enables unit testing of data logic, and prevents template-side tight coupling to data sources.

1.1.3. **SHOULD** limit conditional nesting to maximum 3 levels; deeper logic **SHOULD** be refactored into preparatory PHP code or simplified view model flags.  
**Rationale**: Deep nesting reduces readability and indicates logic leakage from the application layer.

#### 1.2 Template organization

1.2.1. **MUST** organize templates using a consistent directory structure:

```
templates/
├── base/           # Root layouts (base.html.twig)
├── layouts/        # Page-type layouts (dashboard.html.twig)
├── components/     # Reusable UI components (card.html.twig)
├── partials/       # Small fragments (header.html.twig)
└── macros/         # Macro libraries (forms.html.twig)
```

**Rationale**: Predictable locations reduce cognitive load and enable automated tooling to locate templates correctly.

1.2.2. **MUST** use descriptive, kebab-case filenames (e.g., `user-profile-card.html.twig`).  
**Rationale**: Filesystem compatibility and immediate comprehension of template purpose.

1.2.3. **SHOULD** ensure template inheritance depth does not exceed 3 levels (`base` → `layout` → `page`).  
**Rationale**: Excessive inheritance creates "mystery parent" problems where block override behavior becomes unpredictable.

#### 1.3 Composition and reuse

1.3.1. **MUST** use `include` with explicit context isolation when full parent context is not required:

```twig
{{ include('partials/card.html.twig', {title: title, body: body}, with_context = false) }}
```

**Rationale**: Prevents accidental variable leakage and makes dependencies explicit for maintainers.

1.3.2. **MUST** allow-list dynamic template names when the template path originates from user input or CMS content.  
**Rationale**: Prevents file disclosure vulnerabilities and directory traversal via malicious template names.

1.3.3. **SHOULD** use `embed` only when block overrides within the included template are necessary; prefer `include` for static fragments.  
**Rationale**: Overusing `embed` obscures the render flow and complicates debugging.

### 2. Syntax and style

#### 2.1 Automated enforcement

2.1.1. **MUST** enforce style via automation using `twig-cs-fixer` or `twigcs` rather than manual review.  
**Rationale**: Eliminates subjective formatting debates, reduces merge conflicts, and ensures consistency across developer environments and LLM generations.

2.1.2. **MUST** follow official Twig coding standards (key rules summarized below):

- One space after opening and before closing delimiters: `{{ variable }}`, `{% if %}`
- No spaces around whitespace control dashes: `{{- foo -}}`
- One space around binary operators (`+`, `==`, `and`, `~`), **no spaces** around `|`, `.`, `[]`, `..`
- Named arguments use `:` with one space after: `{foo: 'bar'}`
- snake_case for variables, filters, functions, and arguments
- Indent Twig control structures to match the output language indentation (typically 2 spaces for HTML)

**Rationale**: Alignment with upstream Twig project conventions ensures tooling compatibility and reduces friction for developers switching between projects.

2.1.3. **SHOULD** use file extensions that indicate escaping context: `.html.twig` (HTML), `.js.twig` (JavaScript), `.txt.twig` (plain text).  
**Rationale**: Twig and Symfony often select escape strategies based on filename; correct extensions reduce XSS risk through context-aware auto-escaping.

### 3. Security and escaping

#### 3.1 Auto-escaping strategy

3.1.1. **MUST** maintain auto-escaping enabled globally for HTML templates; **MUST NOT** disable globally.  
**Rationale**: Auto-escaping is the primary defense against XSS; disabling it creates systemic vulnerabilities.

3.1.2. **MUST** use context-aware escaping when outputting to non-HTML contexts:

- HTML attributes: `|escape('html_attr')`
- JavaScript: `|escape('js')` or render from `.js.twig`
- CSS: `|escape('css')`
- URLs: `|url_encode`  
  **Rationale**: HTML entity encoding does not protect against injection in JavaScript, CSS, or URL contexts.

  3.1.3. **MUST** treat `|raw` as a security exception requiring justification:

- Only use on data provably safe (sanitized HTML from trusted library, or generated by server-side code)
- **MUST** include inline comment stating the safety contract  
  **Rationale**: `|raw` bypasses all escaping; misuse directly enables XSS attacks.

  3.1.4. **MUST NOT** build inline event handlers (`onclick="{{ ... }}"`) from dynamic strings.  
  **Rationale**: Inline JavaScript contexts are XSS-prone and difficult to escape correctly; use data attributes and external scripts instead.

#### 3.2 Sandboxing

3.2.1. **MUST** enable Twig Sandbox mode when rendering templates authored by untrusted users (CMS, email designers).  
**Rationale**: Sandbox allow-lists tags, filters, and methods, preventing arbitrary code execution and privilege escalation.

3.2.2. **MUST** use allow-list (permitted items) rather than deny-list policies for sandbox configuration.  
**Rationale**: Deny-lists fail when new Twig features are introduced; allow-lists enforce least privilege by default.

### 4. Performance optimization

#### 4.1 Compilation and caching

4.1.1. **MUST** enable Twig compilation cache in production (`cache` directory configured, `auto_reload: false`).  
**Rationale**: Twig compiles to PHP; without caching, templates recompile per request causing $10\times$ to $100\times$ performance degradation.

4.1.2. **SHOULD** enable `strict_variables` in development/test environments to fail fast on missing data.  
**Rationale**: Silent failures in production create "empty UI" bugs; strict mode surfaces contract violations during development.

#### 4.2 Runtime efficiency

4.2.1. **MUST** avoid database queries or expensive operations inside `{% for %}` loops (prevent $N+1$ query problems).  
**Rationale**: Template-side lazy loading creates catastrophic performance cliffs as data scales; eager-load in controllers.

4.2.2. **SHOULD** compute repeated derived values once via `{% set %}` outside loops rather than recalculating per iteration.  
**Rationale**: Reduces redundant CPU cycles and improves template readability.

4.2.3. **SHOULD** minimize filter chains; complex transformations belong in PHP preprocessors.  
**Rationale**: Filter chains execute at runtime; moving work to PHP reduces rendering latency.

### 5. Accessibility and semantic HTML

5.1. **MUST** generate semantic HTML5 landmarks (`<main>`, `<nav>`, `<article>`, `<header>`, `<footer>`) rather than generic `<div>` containers.  
**Rationale**: Screen readers and keyboard navigation depend on semantic structure; fixes are costlier post-deployment.

5.2. **MUST** ensure all form inputs have associated labels (`<label for="id">` or implicit nesting) and error messages linked via `aria-describedby`.  
**Rationale**: WCAG 2.1 Level AA compliance; unlabeled inputs are inaccessible to assistive technologies.

5.3. **MUST** provide appropriate `alt` text for images: descriptive for informative images, empty `alt=""` for decorative.  
**Rationale**: Non-text content requirements for screen reader users.

5.4. **SHOULD** use ARIA attributes sparingly and correctly; prefer native HTML elements where possible.  
**Rationale**: Incorrect ARIA reduces accessibility; native elements have built-in accessibility semantics.

5.5. **SHOULD** ensure interactive controls are keyboard accessible and maintain logical focus order.  
**Rationale**: WCAG operability requirements; prevents excluding keyboard-only users.

### 6. Error handling and robustness

6.1. **MUST** handle missing variables gracefully using `is defined` or `|default()` only for truly optional data; required data **MUST NOT** be silently defaulted to empty strings.  
**Rationale**: Silent defaults hide upstream data contract violations and cause inconsistent UX.

6.2. **SHOULD** use whitespace control (`-`) judiciously to prevent email or HTML layout breaks, but localize trimming to specific tags to avoid unintended inline element collapsing.  
**Rationale**: Whitespace sensitivity in HTML emails and inline layouts causes subtle rendering regressions.

6.3. **MUST** include CI linting (e.g., Symfony `lint:twig` or `twigcs`) to catch syntax errors before deployment.  
**Rationale**: Prevents runtime syntax errors that cause 500 errors in production.

### 7. Internationalization (i18n)

7.1. **SHOULD** wrap all user-facing strings in translation filters (`|trans`) using the platform's i18n extension.  
**Rationale**: Enables localization without template refactoring; hard-coded strings create technical debt for international markets.

7.2. **SHOULD** use placeholder substitution in translation keys rather than string concatenation: `{{ 'welcome_user'|trans({'%name%': user.name}) }}`.  
**Rationale**: Concatenation breaks in languages with different word orders; placeholders allow translators to reorder.

7.3. **SHOULD** format dates, numbers, and currency via locale-aware filters (`format_datetime`, `format_currency`).  
**Rationale**: Ensures correct regional formatting (e.g., decimal separators, date orders) without manual logic.

### 8. Testing and documentation

8.1. **MUST** lint templates in CI/CD using automated tools (`twig-cs-fixer`, `djlint`, or Symfony `lint:twig`).  
**Rationale**: Automated validation prevents style drift and syntax errors from reaching production.

8.2. **SHOULD** document "template APIs" via DocBlock comments at the top of each public template:

- Purpose and expected context variables (names and types)
- Optional vs. required variables
- Security contracts (e.g., "content must be pre-sanitized if rendered raw")  
  **Rationale**: Reduces onboarding time and prevents misuse across teams.

  8.3. **SHOULD** include integration tests for critical templates verifying rendered HTML structure and accessibility attributes.  
  **Rationale**: Prevents regressions in semantic markup and security escaping that visual review misses.

## Appendices

### Appendix A: Application instructions

**When generating new Twig code:**

1. **Clarify context**: Identify template purpose (page, component, email), expected variables (names + types), and inheritance structure.
2. **Apply architecture**: Choose `extends` for pages, `include` for fragments, `embed` for customizable components, `macro` for pure rendering functions.
3. **Enforce security**: Verify autoescape context; require justification comments for any `|raw` usage.
4. **Format automatically**: Ensure code complies with `twig-cs-fixer` rules (spacing, snake_case, indentation).
5. **Document**: Add DocBlock header listing variables and security contracts.
6. **Validate**: Run mental check against Appendix B enforcement checklist.

**When reviewing existing Twig code:**

1. **Output structured compliance report**:
   - **Critical violations**: Security (unescaped output, missing sandbox), MUST standard breaches
   - **Recommendations**: SHOULD/MAY optimizations, performance improvements
   - **Passed**: Standards met
2. **For each violation**, provide:
   - Standard reference (e.g., "Security: Context-aware escaping")
   - Location (file:line)
   - Suggested fix using diff syntax (`---`, `+++`)
3. **Calculate compliance**: `(passed MUST items / total applicable MUST items) × 100%`
4. **Security priority**: If `|raw` is used without justification or autoescape is disabled, prepend ⚠️ **SECURITY WARNING** to response.

### Appendix B: Enforcement checklist

Critical **MUST** items for quick validation:

- [ ] **Logic separation**: No business logic, DB queries, or API calls in templates
- [ ] **Context isolation**: `include` uses `with_context = false` or `only` when appropriate
- [ ] **Dynamic templates**: User-provided template names are allow-listed
- [ ] **Autoescape**: Enabled globally; never disabled at project level
- [ ] **Raw filter**: Exceptional use only, with safety comment explaining sanitization source
- [ ] **Context escaping**: Correct strategy for JS, CSS, URL, and HTML attribute contexts
- [ ] **Sandbox**: Enabled for user-editable templates (CMS, email builders)
- [ ] **Performance**: No lazy-loading/N+1 queries in loops; eager-loaded in controllers
- [ ] **Caching**: Compilation cache enabled in production; `auto_reload` disabled
- [ ] **Linting**: CI pipeline runs `twig-cs-fixer` or `lint:twig`
- [ ] **Accessibility**: Semantic HTML, form labels associated, images have alt text
- [ ] **Naming**: snake_case variables, kebab-case filenames, descriptive block names
- [ ] **Syntax**: One space inside delimiters `{{ }}`, no space around pipes `|`

### Appendix C: Examples

#### C.1 Security: Escaping and `raw` usage

**Non-compliant** (XSS vulnerability, no justification):

```twig
<div class="content">
  {{ article.body|raw }}
</div>

<script>
  var data = {{ user_data }};  {# Injection risk #}
</script>
```

**Compliant** (context-aware, documented):

```twig
<div class="content">
  {# SAFE: article.body_html sanitized server-side via HTMLPurifier #}
  {{ article.body_html|raw }}
</div>

<script>
  {# SAFE: JSON encoding prevents injection #}
  var data = {{ user_data|json_encode|raw }};
</script>
```

#### C.2 Architecture: Separation of concerns

**Non-compliant** (business logic in template, $N+1$ queries):

```twig
{% for category in categories %}
  <h2>{{ category.name }}</h2>
  <ul>
    {% for product in products if product.category_id == category.id %}
      <li>{{ product.name }}</li>
    {% endfor %}
  </ul>
{% endfor %}
```

**Compliant** (prepared view model):

```php
// Controller
$productsByCategory = [];
foreach ($categories as $cat) {
    $productsByCategory[$cat->id] = $productRepository->findByCategory($cat);
}
```

```twig
{% for category_id, products in products_by_category %}
  <section>
    <h2>{{ categories[category_id].name }}</h2>
    <ul>
      {% for product in products %}
        <li>{{ product.name }}</li>
      {% endfor %}
    </ul>
  </section>
{% endfor %}
```

#### C.3 Syntax: Spacing and naming

**Non-compliant**:

```twig
{{variable|upper}}
{%if user.isActive%}
{{ user.userName }}
{{ include('x.html.twig',{foo:'bar'}) }}
```

**Compliant** (per official standards):

```twig
{{ variable|upper }}
{% if user.is_active %}
{{ user.user_name }}
{{ include('x.html.twig', {foo: 'bar'}) }}
```

#### C.4 Accessibility: Forms and ARIA

**Non-compliant**:

```twig
<input type="email" name="email" placeholder="Email">
<div onclick="submit()">Submit</div>
<img src="decorative.png">
```

**Compliant**:

```twig
<label for="email">Email Address</label>
<input type="email" id="email" name="email" autocomplete="email" required>

<button type="submit" aria-label="Submit form">Submit</button>

<img src="decorative.png" alt="" role="presentation">
```
