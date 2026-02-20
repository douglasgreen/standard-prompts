---
name: PHP Project Setup
description: Guidelines document for PHP project setup
version: 1.0.0
modified: 2026-02-20
---
# PHP Project Setup Guide

**Target versions:** PHP 8.3+, Composer 2.6+, PHPUnit 10+, PHPStan 1.10+  
**Reading time:** 25 minutes

---

## Table of contents

1. [Choose your project type](#1-choose-your-project-type)
2. [Common foundation (all projects)](#2-common-foundation-all-projects)
3. [Template A: PHP Library](#3-template-a-php-library)
4. [Template B: Vanilla PHP Web Application](#4-template-b-vanilla-php-web-application)
5. [Template C: Vanilla PHP CLI Application](#5-template-c-vanilla-php-cli-application)
6. [Template D: Symfony Components Project](#6-template-d-symfony-components-project)
7. [Template E: Full Symfony Framework Application](#7-template-e-full-symfony-framework-application)
8. [Quality assurance configuration](#8-quality-assurance-configuration)
9. [Documentation structure](#9-documentation-structure)
10. [Verification checklist](#10-verification-checklist)

---

## 1. Choose your project type

Use this decision matrix to determine which template applies to your use case:

| Scenario | Template | Entry point | Framework |
|----------|----------|-------------|-----------|
| Reusable Composer package | [Library](#3-template-a-php-library) | None | None |
| Simple HTTP API or website | [Vanilla Web](#4-template-b-vanilla-php-web-application) | `public/index.php` | None |
| Command-line tool or worker | [Vanilla CLI](#5-template-c-vanilla-php-cli-application) | `bin/console` | None |
| Custom framework with selected components | [Symfony Components](#6-template-d-symfony-components-project) | `public/index.php` or `bin/console` | Partial |
| Full-featured web application or API | [Full Symfony](#7-template-e-full-symfony-framework-application) | `public/index.php` + `bin/console` | Full Symfony 6.4+ |

> **NOTE:** All templates assume PHP 8.3 or later with strict typing enabled.

---

## 2. Common foundation (all projects)

These steps **MUST** be completed for every PHP project regardless of type.

### 2.1 Initialize repository structure

```bash
mkdir my-project && cd my-project
git init
mkdir -p src tests/{Unit,Integration} docs/{development,architecture} bin public
touch README.md CHANGELOG.md LICENSE .gitignore .editorconfig
```

### 2.2 Configure `.editorconfig`

Create `.editorconfig` to enforce consistent formatting across editors:

```ini
root = true

[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true

[*.php]
indent_style = space
indent_size = 4

[*.{yaml,yml}]
indent_style = space
indent_size = 2

[*.{json,md}]
indent_style = space
indent_size = 2

[Makefile]
indent_style = tab
```

### 2.3 Configure `.gitignore`

Create `.gitignore` with minimum exclusions:

```gitignore
# Dependencies
/vendor/
/node_modules/

# Environment files (never commit secrets)
/.env.local
/.env.*.local
!/.env.example

# Build artifacts
/build/
/.phpunit.cache/
/var/cache/
/var/log/

# IDE files
/.idea/
/.vscode/
*.swp

# Test coverage
/coverage/
/clover.xml
```

### 2.4 Create baseline `composer.json`

Create `composer.json` with common automation scripts:

```json
{
  "name": "vendor/project-name",
  "description": "Project description",
  "type": "project",
  "license": "MIT",
  "require": {
    "php": "^8.3"
  },
  "require-dev": {
    "phpunit/phpunit": "^10.0",
    "phpstan/phpstan": "^1.10",
    "phpstan/phpstan-strict-rules": "^1.5",
    "friendsofphp/php-cs-fixer": "^3.50"
  },
  "autoload": {
    "psr-4": {
      "App\\": "src/"
    }
  },
  "autoload-dev": {
    "psr-4": {
      "App\\Tests\\": "tests/"
    }
  },
  "scripts": {
    "cs:check": "php-cs-fixer fix --diff --dry-run --using-cache=no",
    "cs:fix": "php-cs-fixer fix",
    "stan": "phpstan analyse --configuration=phpstan.dist.neon",
    "test": "phpunit --configuration=phpunit.xml.dist",
    "test:unit": "phpunit --testsuite=Unit",
    "test:integration": "phpunit --testsuite=Integration",
    "audit": "composer audit",
    "qa": [
      "@cs:check",
      "@stan",
      "@test"
    ]
  },
  "config": {
    "sort-packages": true,
    "allow-plugins": {
      "phpstan/extension-installer": true
    }
  }
}
```

> **IMPORTANT:** Run `composer install` after creating this file.

---

## 3. Template A: PHP Library

Use this template for reusable packages published to Packagist or private registries.

### 3.1 Specialize `composer.json`

Update `composer.json` for library distribution:

```json
{
  "name": "vendor/my-library",
  "description": "Reusable library for X",
  "type": "library",
  "license": "MIT",
  "keywords": ["utility", "helper", "psr"],
  "authors": [
    {
      "name": "Your Name",
      "email": "email@example.com"
    }
  ],
  "require": {
    "php": "^8.3"
  },
  "require-dev": {
    "phpunit/phpunit": "^11.0",
    "phpstan/phpstan": "^1.10",
    "phpstan/phpstan-strict-rules": "^1.5",
    "friendsofphp/php-cs-fixer": "^3.50"
  },
  "autoload": {
    "psr-4": {
      "Vendor\\MyLibrary\\": "src/"
    }
  },
  "autoload-dev": {
    "psr-4": {
      "Vendor\\MyLibrary\\Tests\\": "tests/"
    }
  },
  "scripts": {
    "cs:check": "php-cs-fixer fix --diff --dry-run --using-cache=no",
    "cs:fix": "php-cs-fixer fix",
    "stan": "phpstan analyse --configuration=phpstan.dist.neon",
    "test": "phpunit --configuration=phpunit.xml.dist",
    "qa": ["@cs:check", "@stan", "@test"]
  },
  "config": {
    "sort-packages": true
  },
  "minimum-stability": "stable",
  "prefer-stable": true
}
```

### 3.2 Create example library class

Create `src/Calculator.php`:

```php
<?php

declare(strict_types=1);

namespace Vendor\MyLibrary;

final readonly class Calculator
{
    /**
     * Add two integers.
     *
     * @param int $a First operand
     * @param int $b Second operand
     * @return int Sum of operands
     */
    public function add(int $a, int $b): int
    {
        return $a + $b;
    }

    /**
     * Divide two numbers.
     *
     * @param float $dividend Number to be divided
     * @param float $divisor Number to divide by
     * @return float Result of division
     * @throws \InvalidArgumentException When divisor is zero
     */
    public function divide(float $dividend, float $divisor): float
    {
        if (0.0 === $divisor) {
            throw new \InvalidArgumentException('Division by zero is not allowed');
        }

        return $dividend / $divisor;
    }
}
```

### 3.3 Create unit test

Create `tests/Unit/CalculatorTest.php`:

```php
<?php

declare(strict_types=1);

namespace Vendor\MyLibrary\Tests\Unit;

use PHPUnit\Framework\Attributes\CoversClass;
use PHPUnit\Framework\Attributes\DataProvider;
use PHPUnit\Framework\Attributes\Small;
use PHPUnit\Framework\TestCase;
use Vendor\MyLibrary\Calculator;

#[CoversClass(Calculator::class)]
#[Small]
final class CalculatorTest extends TestCase
{
    private Calculator $calculator;

    protected function setUp(): void
    {
        $this->calculator = new Calculator();
    }

    #[DataProvider('additionProvider')]
    public function test_it_adds_two_integers(int $a, int $b, int $expected): void
    {
        // Act
        $result = $this->calculator->add($a, $b);

        // Assert
        $this->assertSame($expected, $result);
    }

    public static function additionProvider(): iterable
    {
        yield 'positive numbers' => [2, 3, 5];
        yield 'negative numbers' => [-2, -3, -5];
        yield 'mixed signs' => [10, -5, 5];
        yield 'zero operand' => [0, 5, 5];
    }

    public function test_it_throws_exception_when_dividing_by_zero(): void
    {
        // Arrange
        $this->expectException(\InvalidArgumentException::class);
        $this->expectExceptionMessage('Division by zero is not allowed');

        // Act
        $this->calculator->divide(10.0, 0.0);
    }
}
```

### 3.4 Verify setup

```bash
composer install
composer qa
```

---

## 4. Template B: Vanilla PHP Web Application

Use this template for simple HTTP APIs or websites without framework dependencies.

### 4.1 Create entry point

Create `public/index.php`:

```php
<?php

declare(strict_types=1);

require_once __DIR__ . '/../vendor/autoload.php';

use App\Http\Kernel;
use App\Http\Request;

// Load environment variables
if (file_exists(__DIR__ . '/../.env')) {
    $lines = file(__DIR__ . '/../.env', FILE_IGNORE_NEW_LINES | FILE_SKIP_EMPTY_LINES);
    foreach ($lines as $line) {
        if (str_starts_with(trim($line), '#')) {
            continue;
        }
        if (str_contains($line, '=')) {
            putenv($line);
        }
    }
}

// Create request from superglobals
$request = Request::fromGlobals();

// Handle via kernel
$kernel = new Kernel();
$response = $kernel->handle($request);

// Send response
http_response_code($response->statusCode);
foreach ($response->headers as $name => $value) {
    header("{$name}: {$value}");
}
echo $response->body;
```

### 4.2 Create HTTP abstractions

Create `src/Http/Request.php`:

```php
<?php

declare(strict_types=1);

namespace App\Http;

final readonly class Request
{
    public function __construct(
        public string $method,
        public string $path,
        public array $query,
        public array $post,
        public array $server,
    ) {
    }

    public static function fromGlobals(): self
    {
        return new self(
            method: $_SERVER['REQUEST_METHOD'] ?? 'GET',
            path: parse_url($_SERVER['REQUEST_URI'] ?? '/', PHP_URL_PATH) ?? '/',
            query: $_GET,
            post: $_POST,
            server: $_SERVER,
        );
    }
}
```

Create `src/Http/Response.php`:

```php
<?php

declare(strict_types=1);

namespace App\Http;

final readonly class Response
{
    /**
     * @param array<string, string> $headers
     */
    public function __construct(
        public int $statusCode = 200,
        public string $body = '',
        public array $headers = ['Content-Type' => 'text/html; charset=UTF-8'],
    ) {
    }

    public static function json(array $data, int $statusCode = 200): self
    {
        return new self(
            statusCode: $statusCode,
            body: json_encode($data, JSON_THROW_ON_ERROR),
            headers: ['Content-Type' => 'application/json'],
        );
    }
}
```

Create `src/Http/Kernel.php`:

```php
<?php

declare(strict_types=1);

namespace App\Http;

final class Kernel
{
    public function handle(Request $request): Response
    {
        return match ($request->path) {
            '/health' => Response::json(['status' => 'ok']),
            '/' => new Response(body: 'Hello World'),
            default => Response::json(['error' => 'Not Found'], 404),
        };
    }
}
```

### 4.3 Add web server configuration

For Apache, create `public/.htaccess`:

```apache
<IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteRule ^(.*)$ index.php [QSA,L]
</IfModule>
```

For Nginx, configure server block:

```nginx
server {
    listen 80;
    server_name myproject.local;
    root /var/www/myproject/public;

    location / {
        try_files $uri /index.php$is_args$args;
    }

    location ~ ^/index\.php(/|$) {
        fastcgi_pass unix:/var/run/php/php8.3-fpm.sock;
        fastcgi_split_path_info ^(.+\.php)(/.*)$;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        fastcgi_param DOCUMENT_ROOT $realpath_root;
        internal;
    }

    location ~ \.php$ {
        return 404;
    }
}
```

### 4.4 Create functional test

Create `tests/Functional/HttpKernelTest.php`:

```php
<?php

declare(strict_types=1);

namespace App\Tests\Functional;

use App\Http\Kernel;
use App\Http\Request;
use PHPUnit\Framework\Attributes\CoversClass;
use PHPUnit\Framework\Attributes\Large;
use PHPUnit\Framework\TestCase;

#[CoversClass(Kernel::class)]
#[Large]
final class HttpKernelTest extends TestCase
{
    public function test_health_endpoint_returns_ok(): void
    {
        // Arrange
        $kernel = new Kernel();
        $request = new Request('GET', '/health', [], [], []);

        // Act
        $response = $kernel->handle($request);

        // Assert
        $this->assertSame(200, $response->statusCode);
        $this->assertSame('{"status":"ok"}', $response->body);
    }

    public function test_unknown_path_returns_404(): void
    {
        // Arrange
        $kernel = new Kernel();
        $request = new Request('GET', '/unknown', [], [], []);

        // Act
        $response = $kernel->handle($request);

        // Assert
        $this->assertSame(404, $response->statusCode);
    }
}
```

---

## 5. Template C: Vanilla PHP CLI Application

Use this template for command-line tools, workers, or cron jobs without framework dependencies.

### 5.1 Create entry point

Create `bin/console`:

```php
#!/usr/bin/env php
<?php

declare(strict_types=1);

require_once __DIR__ . '/../vendor/autoload.php';

use App\Cli\Application;

$app = new Application();
exit($app->run($argv));
```

Make it executable:

```bash
chmod +x bin/console
```

### 5.2 Create CLI application

Create `src/Cli/Application.php`:

```php
<?php

declare(strict_types=1);

namespace App\Cli;

final class Application
{
    /**
     * @param array<int, string> $argv
     */
    public function run(array $argv): int
    {
        $command = $argv[1] ?? 'help';

        return match ($command) {
            'greet' => $this->greet($argv[2] ?? 'World'),
            'help' => $this->showHelp(),
            default => $this->unknownCommand($command),
        };
    }

    private function greet(string $name): int
    {
        fwrite(STDOUT, "Hello, {$name}!\n");

        return 0;
    }

    private function showHelp(): int
    {
        fwrite(STDOUT, "Available commands:\n");
        fwrite(STDOUT, "  greet [name]  Greet someone\n");
        fwrite(STDOUT, "  help          Show this help\n");

        return 0;
    }

    private function unknownCommand(string $command): int
    {
        fwrite(STDERR, "Unknown command: {$command}\n");

        return 1;
    }
}
```

### 5.3 Update `composer.json`

Add the binary to `composer.json`:

```json
{
  "bin": ["bin/console"]
}
```

### 5.4 Create functional test

Create `tests/Functional/CliApplicationTest.php`:

```php
<?php

declare(strict_types=1);

namespace App\Tests\Functional;

use PHPUnit\Framework\Attributes\Large;
use PHPUnit\Framework\TestCase;

#[Large]
final class CliApplicationTest extends TestCase
{
    public function test_cli_outputs_greeting(): void
    {
        // Arrange
        $command = escapeshellcmd(PHP_BINARY) . ' ' . escapeshellarg(__DIR__ . '/../../bin/console') . ' greet Test';

        // Act
        exec($command, $output, $exitCode);

        // Assert
        $this->assertSame(0, $exitCode);
        $this->assertSame(['Hello, Test!'], $output);
    }

    public function test_cli_returns_error_for_unknown_command(): void
    {
        // Arrange
        $command = escapeshellcmd(PHP_BINARY) . ' ' . escapeshellarg(__DIR__ . '/../../bin/console') . ' unknown';

        // Act
        exec($command, $output, $exitCode);

        // Assert
        $this->assertSame(1, $exitCode);
    }
}
```

---

## 6. Template D: Symfony Components Project

Use this template when you need specific Symfony components (HTTP Foundation, Routing, Console) without the full framework.

### 6.1 Install dependencies

```bash
composer require symfony/http-foundation symfony/routing symfony/http-kernel
composer require symfony/console symfony/dependency-injection symfony/config symfony/yaml
composer require --dev symfony/var-dumper
```

### 6.2 Create web entry point

Create `public/index.php`:

```php
<?php

declare(strict_types=1);

use App\Http\ComponentKernel;
use Symfony\Component\HttpFoundation\Request;

require_once __DIR__ . '/../vendor/autoload.php';

$request = Request::createFromGlobals();
$kernel = new ComponentKernel();
$response = $kernel->handle($request);
$response->send();
```

### 6.3 Create component kernel

Create `src/Http/ComponentKernel.php`:

```php
<?php

declare(strict_types=1);

namespace App\Http;

use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\Routing\Matcher\UrlMatcher;
use Symfony\Component\Routing\RequestContext;
use Symfony\Component\Routing\Route;
use Symfony\Component\Routing\RouteCollection;

final class ComponentKernel
{
    public function handle(Request $request): Response
    {
        $routes = $this->createRoutes();
        $context = new RequestContext();
        $context->fromRequest($request);
        $matcher = new UrlMatcher($routes, $context);

        try {
            $parameters = $matcher->match($request->getPathInfo());
            
            return match ($parameters['_controller']) {
                'health' => new Response('OK', 200, ['Content-Type' => 'text/plain']),
                default => new Response('Not Found', 404),
            };
        } catch (\Exception $e) {
            return new Response('Not Found', 404);
        }
    }

    private function createRoutes(): RouteCollection
    {
        $routes = new RouteCollection();
        $routes->add('health', new Route('/health', ['_controller' => 'health']));

        return $routes;
    }
}
```

### 6.4 Create CLI entry point

Create `bin/console`:

```php
#!/usr/bin/env php
<?php

declare(strict_types=1);

use App\Cli\ApplicationFactory;

require __DIR__ . '/../vendor/autoload.php';

exit(ApplicationFactory::create()->run());
```

Create `src/Cli/ApplicationFactory.php`:

```php
<?php

declare(strict_types=1);

namespace App\Cli;

use Symfony\Component\Console\Application;

final class ApplicationFactory
{
    public static function create(): Application
    {
        $app = new Application('app', '1.0.0');
        $app->add(new Command\HelloCommand());

        return $app;
    }
}
```

Create `src/Cli/Command/HelloCommand.php`:

```php
<?php

declare(strict_types=1);

namespace App\Cli\Command;

use Symfony\Component\Console\Command\Command;
use Symfony\Component\Console\Input\InputInterface;
use Symfony\Component\Console\Output\OutputInterface;

final class HelloCommand extends Command
{
    protected static $defaultName = 'app:hello';

    protected function execute(InputInterface $input, OutputInterface $output): int
    {
        $output->writeln('Hello from Symfony Console!');

        return Command::SUCCESS;
    }
}
```

### 6.5 Configure PHPStan for Symfony

Install Symfony extension:

```bash
composer require --dev phpstan/phpstan-symfony
```

Update `phpstan.dist.neon`:

```neon
includes:
  - vendor/phpstan/phpstan-symfony/extension.neon

parameters:
  level: 9
  paths:
    - src
    - tests
  symfony:
    container_xml_path: var/cache/dev/App_KernelDevDebugContainer.xml
```

---

## 7. Template E: Full Symfony Framework Application

Use this template for production web applications requiring the full Symfony framework (ORM, security, forms, etc.).

### 7.1 Create project

Using Symfony CLI (recommended):

```bash
symfony new my-app --version=7.0 --webapp
cd my-app
```

Or using Composer:

```bash
composer create-project symfony/skeleton:"^7.0" my-app
cd my-app
composer require webapp
```

### 7.2 Enforce PHP 8.3+

Update `composer.json`:

```json
{
  "require": {
    "php": ">=8.3",
    "ext-ctype": "*",
    "ext-iconv": "*"
  }
}
```

Run `composer update` after modifying.

### 7.3 Install quality assurance tools

```bash
composer require --dev phpstan/phpstan phpstan/phpstan-strict-rules phpstan/phpstan-symfony phpstan/phpstan-doctrine
composer require --dev friendsofphp/php-cs-fixer
composer require --dev phpunit/phpunit symfony/test-pack dama/doctrine-test-bundle
```

### 7.4 Configure PHPStan

Create `phpstan.dist.neon`:

```neon
includes:
  - vendor/phpstan/phpstan-symfony/extension.neon
  - vendor/phpstan/phpstan-doctrine/extension.neon

parameters:
  level: 9
  paths:
    - src
    - tests
  excludePaths:
    - tests/bootstrap.php
    - var/
  symfony:
    container_xml_path: var/cache/dev/App_KernelDevDebugContainer.xml
  doctrine:
    objectManagerLoader: tests/object-manager.php
  checkMissingIterableValueType: true
  reportUnmatchedIgnoredErrors: true
```

### 7.5 Configure PHP CS Fixer

Create `.php-cs-fixer.dist.php`:

```php
<?php

declare(strict_types=1);

use PhpCsFixer\Config;
use PhpCsFixer\Finder;

$finder = (new Finder())
    ->in(__DIR__)
    ->exclude(['var', 'vendor', 'node_modules'])
    ->ignoreDotFiles(true)
    ->ignoreVCSIgnored(true);

return (new Config())
    ->setRiskyAllowed(true)
    ->setFinder($finder)
    ->setRules([
        '@PSR12' => true,
        '@Symfony' => true,
        'declare_strict_types' => true,
        'strict_param' => true,
        'strict_comparison' => true,
        'trailing_comma_in_multiline' => true,
        'yoda_style' => ['equal' => true, 'identical' => true, 'less_and_greater' => false],
    ]);
```

### 7.6 Update Composer scripts

Add to `composer.json`:

```json
{
  "scripts": {
    "cs:check": "php-cs-fixer fix --dry-run --diff",
    "cs:fix": "php-cs-fixer fix",
    "stan": "phpstan analyse --configuration=phpstan.dist.neon",
    "test": "bin/phpunit",
    "test:unit": "bin/phpunit --testsuite=Unit",
    "test:integration": "bin/phpunit --testsuite=Integration",
    "test:functional": "bin/phpunit --testsuite=Functional",
    "qa": [
      "@cs:check",
      "@stan",
      "@test"
    ]
  }
}
```

### 7.7 Configure test suites

Update `phpunit.xml.dist`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<phpunit xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="vendor/phpunit/phpunit/phpunit.xsd"
         bootstrap="tests/bootstrap.php"
         colors="true"
         cacheDirectory=".phpunit.cache"
         executionOrder="random"
         failOnRisky="true"
         failOnWarning="true">

  <testsuites>
    <testsuite name="Unit">
      <directory>tests/Unit</directory>
    </testsuite>
    <testsuite name="Integration">
      <directory>tests/Integration</directory>
    </testsuite>
    <testsuite name="Functional">
      <directory>tests/Functional</directory>
    </testsuite>
  </testsuites>

  <source>
    <include>
      <directory suffix=".php">src</directory>
    </include>
  </source>

  <php>
    <ini name="error_reporting" value="-1"/>
    <server name="APP_ENV" value="test" force="true"/>
    <server name="SHELL_VERBOSITY" value="-1"/>
  </php>
</phpunit>
```

### 7.8 Security configuration

Ensure `.env.local` is gitignored and use Symfony Secrets for production:

```bash
# Generate key pair for secrets
php bin/console secrets:generate-keys

# Set a secret
php bin/console secrets:set DATABASE_PASSWORD
```

> **WARNING:** Never commit `.env.local` or decryption keys to version control.

---

## 8. Quality assurance configuration

These configurations **MUST** be applied to all project types.

### 8.1 PHP CS Fixer (`.php-cs-fixer.dist.php`)

```php
<?php

declare(strict_types=1);

use PhpCsFixer\Config;
use PhpCsFixer\Finder;

$finder = (new Finder())
    ->in([__DIR__ . '/src', __DIR__ . '/tests'])
    ->exclude(['var', 'vendor'])
    ->ignoreDotFiles(true)
    ->ignoreVCSIgnored(true);

return (new Config())
    ->setRiskyAllowed(true)
    ->setFinder($finder)
    ->setRules([
        '@PSR12' => true,
        'declare_strict_types' => true,
        'strict_param' => true,
        'strict_comparison' => true,
        'no_unused_imports' => true,
        'ordered_imports' => ['sort_algorithm' => 'alpha'],
        'array_syntax' => ['syntax' => 'short'],
        'trailing_comma_in_multiline' => [
            'elements' => ['arrays', 'arguments', 'parameters'],
        ],
        'yoda_style' => [
            'equal' => true,
            'identical' => true,
            'less_and_greater' => false,
        ],
    ]);
```

### 8.2 PHPStan (`phpstan.dist.neon`)

```neon
parameters:
  level: 9
  paths:
    - src
    - tests
  excludePaths:
    - vendor
    - var
    - node_modules
  checkMissingIterableValueType: true
  checkGenericClassInNonGenericObjectType: true
  reportUnmatchedIgnoredErrors: true
  tmpDir: var/cache/phpstan
```

### 8.3 PHPUnit (`phpunit.xml.dist`)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<phpunit xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="vendor/phpunit/phpunit/phpunit.xsd"
         bootstrap="vendor/autoload.php"
         colors="true"
         cacheDirectory=".phpunit.cache"
         executionOrder="random"
         failOnRisky="true"
         failOnWarning="true"
         beStrictAboutOutputDuringTests="true"
         requireCoverageMetadata="false">

  <testsuites>
    <testsuite name="Unit">
      <directory>tests/Unit</directory>
    </testsuite>
    <testsuite name="Integration">
      <directory>tests/Integration</directory>
    </testsuite>
  </testsuites>

  <source>
    <include>
      <directory suffix=".php">src</directory>
    </include>
  </source>

  <php>
    <ini name="error_reporting" value="-1"/>
    <ini name="display_errors" value="1"/>
  </php>
</phpunit>
```

### 8.4 Node.js `package.json` (optional)

For documentation linting and formatting:

```json
{
  "name": "@vendor/project",
  "private": true,
  "devDependencies": {
    "markdownlint-cli2": "^0.14.0",
    "prettier": "^3.3.0"
  },
  "scripts": {
    "fmt": "prettier -w .",
    "lint:md": "markdownlint-cli2 \"**/*.md\" \"!vendor/**\" \"!node_modules/**\""
  }
}
```

---

## 9. Documentation structure

All projects **MUST** include this documentation structure following the Diátaxis framework :

```
docs/
├── index.md                    # Documentation landing page
├── architecture.md             # System architecture explanation
├── development/
│   ├── setup.md               # Tutorial: environment setup
│   └── testing.md             # How-to: running tests
├── api/
│   └── reference.md           # API reference (auto-generated or manual)
└── adr/                       # Architecture Decision Records
    └── 0001-record-decisions.md
```

### 9.1 `README.md` template

```markdown
# Project Name

Brief description of the project.

## Requirements

- PHP 8.3 or later
- Composer 2.6 or later

## Installation

```bash
composer install
```

## Quality assurance

```bash
composer qa
```

## Documentation

See `docs/index.md` for complete documentation.
```

### 9.2 `docs/index.md` template

```markdown
---
title: Project Documentation
description: Complete documentation for the project
last_updated: 2026-02-08
---

# Project Documentation

## Tutorials
- [Development setup](development/setup.md)

## How-to guides
- [Running tests](development/testing.md)

## Reference
- [API documentation](api/reference.md)
- [CHANGELOG.md](../CHANGELOG.md)

## Explanation
- [Architecture overview](architecture.md)
- [Architecture Decision Records](adr/)
```

---

## 10. Verification checklist

Before committing any new PHP project, verify compliance with these **MUST** items:

### 10.1 File structure

- [ ] `composer.json` requires `"php": "^8.3"`
- [ ] `composer.json` uses PSR-4 autoloading
- [ ] `.gitignore` excludes `vendor/`, `.env.local`, and IDE files
- [ ] `.editorconfig` exists and defines PHP spacing (4 spaces)
- [ ] `README.md` includes installation and QA instructions
- [ ] `docs/` directory exists with `index.md`

### 10.2 Code standards

- [ ] All PHP files begin with `declare(strict_types=1);`
- [ ] All functions have explicit parameter and return types
- [ ] No business logic in entry points (`public/index.php`, `bin/*`)
- [ ] Constructor injection used throughout (no service locator)
- [ ] No `@` error suppression operators

### 10.3 Quality assurance

- [ ] `composer qa` runs successfully (cs:check, stan, test)
- [ ] PHPStan configured at level 8+ (9 preferred)
- [ ] PHP CS Fixer configured with PSR-12 + strict rules
- [ ] PHPUnit 10+ with testsuites for Unit/Integration
- [ ] `composer audit` shows no vulnerabilities

### 10.4 Security

- [ ] `.env.local` in `.gitignore`
- [ ] No secrets in code (use `<YOUR_API_KEY>` placeholders)
- [ ] Input validation at application boundaries
- [ ] Prepared statements for database queries (if applicable)

### 10.5 Automation script

Create `bin/validate-setup` for automated verification:

```bash
#!/usr/bin/env bash
set -e

echo "=== PHP Project Setup Validation ==="

# Check PHP version
PHP_VERSION=$(php -r 'echo PHP_VERSION_ID;')
if [ "$PHP_VERSION" -ge 80300 ]; then
    echo "✓ PHP 8.3+ detected"
else
    echo "✗ PHP 8.3+ required"
    exit 1
fi

# Verify required files
FILES=("composer.json" "README.md" ".gitignore" ".editorconfig" "phpunit.xml.dist" "phpstan.dist.neon" ".php-cs-fixer.dist.php")
for file in "${FILES[@]}"; do
    if [ -f "$file" ]; then
        echo "✓ $file exists"
    else
        echo "✗ Missing: $file"
        exit 1
    fi
done

# Run quality checks
echo ""
echo "Running quality checks..."
composer qa && echo "✓ All quality checks passed" || { echo "✗ Quality checks failed"; exit 1; }

echo ""
echo "=== ✓ All validations passed ==="
```

```bash
chmod +x bin/validate-setup
./bin/validate-setup
```

---

## Summary

You now have complete setup instructions for:

1. **PHP Libraries** — Reusable Composer packages with strict typing
2. **Vanilla PHP Web Apps** — Custom HTTP handling without frameworks
3. **Vanilla PHP CLI Apps** — Command-line tools with clean architecture
4. **Symfony Components Projects** — Selective framework usage
5. **Full Symfony Applications** — Production-ready web apps with complete tooling

All templates enforce:
- **PHP 8.3+** with `declare(strict_types=1);`
- **PSR-4 autoloading** and **PSR-12 coding standards**
- **PHPStan level 9** static analysis
- **PHPUnit 10+** with attributes and strict configuration
- **Automated QA** via Composer scripts
- **Security best practices** (no committed secrets, input validation)

**Next steps:**
1. Select your project type from the decision matrix
2. Apply the corresponding template configuration
3. Run `composer install` and `composer qa`
4. Execute `./bin/validate-setup` to verify compliance
5. Begin development with confidence in your engineering standards
