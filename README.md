# Standard prompts

The standard prompts project is a set of LLM prompts developed using chatbots with reasoning and web search enabled.

Prompts were written using the [prompt.md](prompt template) to make the requests. When using the template:
1. Replace [technology] with the brief name of the technology. Expand the first use of the term with a more detailed description if needed for clarity.
2. Replace [questions] with a list of questions about the technology. For example, I ask Grok, "Make a list of questions to be answered in a definition of standards or best practices for [technology]".
3. Describe the Exclusions line in the Scope and Limitations section to exclude other related documents so they don't get repeated.
4. Include a copy of our [general/technical-writing.md](technical writing) standards to produce the correct style.

## Project organization

### Standards

Project are grouped into directories by technology, where:
* "general" means applies to everything
* "other" means a specific other technology not in its own group.

* [general/accessibility.md](Accessibility)
* [general/architecture.md](Architecture)
* [general/chatbot.md](Chatbot)
* [general/cli.md](CLI)
* [general/cryptography.md](Cryptography)
* [general/datetime-handling.md](Datetime handling)
* [general/design-pattern.md](Design pattern)
* [general/dev-spec.md](Developer specification)
* [general/error-handling.md](Error handling)
* [general/i18n-and-l10n.md.md](Internationalization and localization)
* [general/naming-glossary.md](Naming glossary)
* [general/project-docs.md](Project documentation)
* [general/security-and-privacy.md](Security and privacy)
* [general/technical-writing.md](Technical writing)
* [general/test-spec.md](Test specification)
* [general/upgrade.md](Upgrade)
* [general/ux.md](UX)
* [html-css/bootstrap.md](Bootstrap)
* [html-css/html-css.md](HTML/CSS)
* [html-css/twig.md](Twig)
* [javascript/javascript.md](JavaScript)
* [javascript/nodejs.md](Node.js)
* [javascript/playwright.md](Playwright)
* [javascript/vitest.md](Vitest)
* [javascript/vuejs.md](Vue.js)
* [other/bash-scripting.md](Bash scripting) - needs revision
* [other/gitlab-ci-cd.md](GitLab CI/CD)
* [other/go.md](Go)
* [other/java.md](Java)
* [other/mermaid.md](Mermaid)
* [other/python.md](Python)
* [other/xml.md](XML)
* [php/cookie.md](Cookie)
* [php/phpdoc.md](PHPDoc)
* [php/php.md](PHP)
* [php/phpunit.md](PHPUnit)
* [php/session.md](Session)
* [php/symfony.md](Symfony)
* [sql/schema.md](SQL schemas)
* [sql/query.md](SQL queries)

#### To do

Add standards for:
* Agile
* Cloud Computing
* Docker/containerization
* Environment configuration
* Logging & monitoring
* Performance optimization
* Software requirements + Prototyping (mockups) guidelines
* REST APIs
* xAPI learning statements

Remove the "Document Conventions" section of each document.

Remove redundant style config files from examples and combine into one complete example.

### Guidelines

Guidelines are step-by-step how-to guides written in support of the standards. 

* [guidelines/java-project-setup.md](Java project setup)
* [guidelines/php-project-setup.md](PHP project setup)
* [guidelines/python-project-setup.md](Python project setup)
