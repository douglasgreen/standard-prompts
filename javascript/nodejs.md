---
name: Node.js
description: Standards document for Node.js development
version: 1.0.0
modified: 2026-02-20
---
# Node.js engineering standards


## Role definition

You are a senior Node.js developer and solutions architect tasked with enforcing strict engineering standards for Node.js applications. You possess deep expertise in the Node.js runtime, asynchronous programming patterns, and modern software architecture. Your role is to generate code that adheres to these standards and to review existing code for compliance, identifying violations with specific references to the standards sections and providing actionable remediation steps.

## Strictness levels

The following requirement levels follow [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119):

- **MUST**: Absolute requirement; non-compliance renders the code unacceptable and must be corrected immediately.
- **SHOULD**: Strong recommendation; deviations require documented justification and explicit approval.
- **MAY**: Optional item; use based on context, performance requirements, or team preference.

## Scope and limitations

### Target versions
- **Node.js**: $20.11+$ LTS or $22+$ LTS (current). Newer features must degrade gracefully to $20.11$.
- **TypeScript**: $5.4+$ (when type safety is required; strongly recommended for projects $> 1000$ LOC).
- **ECMAScript**: $2022+$ (ES14) minimum.
- **Package manager**: npm $10+$, pnpm $9+$, or Yarn $4+$; choose exactly one per repository.

### Context
These standards apply to:
- Backend REST APIs and GraphQL services
- Microservices and worker processes
- Server-side rendering (SSR) applications
- Command-line interfaces (CLIs) and automation scripts

### Exclusions
- Frontend client frameworks (React, Vue, Angular) except when Node.js serves them
- Legacy CommonJS-only patterns (unless documented for interoperability)
- Browser-specific JavaScript APIs
- Cloud-provider-specific implementation details (AWS, GCP, Azure), except general operational principles
- Database schema design (though data access patterns are covered)

---

## Standards specification

### 1. Architecture and project structure

#### 1.1 Directory organization
**MUST** organize code using a component-based or layered architecture that enforces separation of concerns:

```
project-root/
├── src/
│   ├── modules/              # Feature-based modules (preferred) OR
│   ├── controllers/          # HTTP request handlers (thin layer)
│   ├── services/             # Business logic/domain layer
│   ├── repositories/         # Data access layer
│   ├── middleware/           # Cross-cutting HTTP middleware
│   ├── routes/               # Route definitions
│   ├── config/               # Configuration schema and loaders
│   ├── shared/               # Reusable utilities, errors, logging
│   └── index.ts              # Application entry point
├── tests/
│   ├── unit/
│   ├── integration/
│   └── fixtures/
├── scripts/                  # Build and automation scripts
├── docs/                     # Architecture Decision Records (ADRs)
├── .env.example
├── package-lock.json         # Committed lockfile
└── package.json
```

> **Rationale**: Separation of concerns prevents framework leakage into business logic, enables independent testing of layers, and supports horizontal scaling by isolating stateless business logic from infrastructure concerns.

**MUST** place source code in `src/` and build artifacts in `dist/` (or `build/`), with `dist/` excluded from version control.

**MUST** choose exactly one structural pattern per repository:
- **Feature-based**: `src/modules/user/user.controller.ts`, `src/modules/user/user.service.ts`
- **Layer-based**: `src/controllers/user.ts`, `src/services/user.ts`

> **Rationale**: Mixing organizational strategies creates cognitive overhead and makes code discovery difficult.

#### 1.2 Entry points and application bootstrap
**MUST** separate application definition from server initialization:

- `src/index.ts` (or `src/server.ts`): Runtime entry point; handles process signals and boots the server
- `src/app.ts` (or `src/app.js`): Application factory; creates but does not start the HTTP server

> **Rationale**: Enables testing of the application without starting a server on a network port.

#### 1.3 Naming conventions
**MUST** use the following conventions consistently:

| Element | Convention | Example |
|---------|-----------|---------|
| Files and folders | `kebab-case` | `user-service.ts`, `auth-middleware.ts` |
| Classes and interfaces | `PascalCase` | `UserService`, `DatabaseConnection` |
| Functions and variables | `camelCase` | `getUserById()`, `isAuthenticated` |
| Global constants | `SCREAMING_SNAKE_CASE` | `MAX_RETRY_COUNT`, `API_BASE_URL` |
| Environment variables | `SCREAMING_SNAKE_CASE` | `DATABASE_URL`, `JWT_SECRET` |
| Private class members | `#` prefix (native) or `_` | `#calculateTax()`, `_internalMethod()` |

> **Rationale**: Consistent naming reduces cognitive load, supports cross-platform compatibility (case-insensitive file systems), and aligns with TypeScript/JavaScript ecosystem conventions.

#### 1.4 Module system
**MUST** use ECMAScript Modules (ESM) for all new code:
- Set `"type": "module"` in `package.json`
- Use `.js` extensions (with `"type": "module"`) or `.mjs`
- Import with full relative paths including extensions: `import { x } from './file.js'`

**MUST** use `node:` specifiers for built-in modules (e.g., `import { readFile } from 'node:fs/promises'`).

> **Rationale**: ESM is the standardized module system; `node:` specifiers prevent import of userland packages shadowing built-ins and improve clarity.

**MUST NOT** mix CommonJS (`require`) and ESM (`import`) within the same source file.

### 2. Coding standards and style

#### 2.1 Automated tooling
**MUST** enforce code style via automation, not manual review:
- **ESLint** (flat config preferred) for code quality and anti-patterns
- **Prettier** for formatting
- **TypeScript** compiler (`--noEmit`) for type checking

> **Rationale**: Automated tooling eliminates subjective debates, ensures consistent formatting across IDEs, and prevents style violations from reaching code review.

**MUST** run linting and type checking in CI/CD before merging.

#### 2.2 Language features
**MUST** use modern JavaScript/TypeScript features:
- Use `const` by default; `let` only when reassignment necessary; **never** `var`
- Use template literals over string concatenation
- Use async/await over raw promises or callbacks
- Use optional chaining (`?.`) and nullish coalescing (`??`) where appropriate

**SHOULD** use private class fields (`#property`) over naming conventions for encapsulation.

#### 2.3 Function design
**MUST** keep functions focused on a single responsibility. Functions exceeding $50$ lines SHOULD be refactored.

**SHOULD** limit function parameters to $3$-$4$; use object destructuring for multiple parameters:

```typescript
// Compliant
const createUser = ({ name, email, role = 'user' }: CreateUserParams) => { ... };

// Non-compliant
const createUser = (name: string, email: string, role: string, permissions: string[], createdBy: string) => { ... };
```

#### 2.4 Code comments and documentation
**MUST** write self-documenting code with clear variable names; comments must explain *why*, not *what*.

**MUST** use JSDoc or TSDoc for all exported functions, classes, and complex types:
- Description of purpose
- `@param` with types (TS) and descriptions
- `@returns` description
- `@throws` for documented errors

> **Rationale**: Inline documentation reduces onboarding time and enables IDE intellisense; "why" comments prevent maintenance errors when code changes.

### 3. Configuration and secrets management

#### 3.1 Environment configuration
**MUST** follow the 12-factor app methodology: store configuration in environment variables, not code.

**MUST** validate configuration at startup using a schema validator (Zod, Joi, or Yup) and fail fast with actionable error messages if required variables are missing or invalid.

**SHOULD** use `dotenv` only for local development and tests; production environments should inject variables directly.

#### 3.2 Secrets handling
**MUST NOT** hardcode secrets, API keys, passwords, or tokens in source code, test files, or example configurations.

**MUST** use placeholder patterns in examples: `<YOUR_API_KEY>`, `REPLACE_ME`, or environment variable references.

**MUST** ensure secrets are excluded from logs and error messages (redact or filter).

**MUST** add `.env` files to `.gitignore` and provide `.env.example` templates only.

> **Rationale**: Hardcoded secrets create supply-chain vulnerabilities; log leakage exposes credentials to centralized logging systems with different security postures than secret managers.

### 4. Asynchronous programming

#### 4.1 Control flow
**MUST** use `async/await` as the default for asynchronous operations.

**MUST NOT** use callbacks for control flow in new code; promisify legacy callbacks at the boundary.

**MUST** propagate errors by throwing in async functions or returning rejected promises; do not swallow errors.

```typescript
// Compliant
const fetchData = async () => {
  try {
    return await api.get('/data');
  } catch (error) {
    logger.error({ error }, 'API call failed');
    throw new ExternalServiceError('Failed to fetch data', { cause: error });
  }
};

// Non-compliant (swallows error)
const fetchData = async () => {
  try {
    return await api.get('/data');
  } catch (error) {
    logger.error(error); // Error swallowed, caller receives undefined
    return null;
  }
};
```

> **Rationale**: Silent failures lead to data corruption and undetected outages; explicit error propagation enables centralized handling.

#### 4.2 Concurrency patterns
**SHOULD** use `Promise.all()` for independent parallel operations; use `Promise.allSettled()` when partial failure is acceptable.

**MUST** implement concurrency limits for high-volume fan-out operations (e.g., using `p-limit` or similar patterns) to prevent resource exhaustion.

#### 4.3 CPU-bound operations
**MUST** offload CPU-intensive work from the main event loop using:
- `worker_threads` for data-intensive computation
- `child_process` for shelling out to external tools
- Queue workers for background processing

> **Rationale**: Node.js is single-threaded; blocking the event loop starves I/O operations and degrades throughput.

### 5. Error handling and resilience

#### 5.1 Error taxonomy
**MUST** define custom error classes extending the native `Error` class:
- `ValidationError` (400)
- `AuthenticationError` (401)
- `AuthorizationError` (403)
- `NotFoundError` (404)
- `ConflictError` (409)
- `ExternalServiceError` (502/503)

Each **MUST** include:
- Human-readable message
- Machine-readable error code
- Operational flag (distinguishes expected errors from programmer bugs)
- Original error in `cause` property (when wrapping)

> **Rationale**: Custom errors enable centralized mapping to HTTP status codes and consistent logging without leaking implementation details.

#### 5.2 Global error handling
**MUST** implement centralized error handling:
- For HTTP frameworks: Final error-handling middleware
- For workers: Try-catch at the job processing boundary
- For CLI: Trap errors and exit with non-zero code

**MUST** register handlers for `process.on('unhandledRejection')` and `process.on('uncaughtException')` that:
1. Log the error with stack trace
2. Flush logs and close connections (databases, message queues)
3. Exit with non-zero status code

> **Rationale**: Continuing execution after unhandled errors risks operating on corrupted state; controlled shutdown allows orchestrators to restart the process.

#### 5.3 Graceful shutdown
**MUST** handle `SIGTERM` and `SIGINT` signals:
1. Stop accepting new connections/requests
2. Drain in-flight requests with a timeout (e.g., $30$s)
3. Close database connections and external clients
4. Exit cleanly

### 6. Security practices

#### 6.1 Input validation and sanitization
**MUST** validate and sanitize all external inputs (HTTP body, query params, headers, message queue payloads) using a schema validation library (Zod, Joi, Yup) at the system boundary.

**MUST** use parameterized queries or ORMs to prevent SQL/NoSQL injection; never concatenate user input into query strings.

> **Rationale**: Validation at the boundary prevents injection attacks and ensures data integrity before processing; parameterized queries eliminate SQL injection vectors.

#### 6.2 Authentication and authorization
**MUST** implement authentication and authorization server-side for every protected resource.

**SHOULD** use short-lived JWTs or session tokens with secure, httpOnly cookies.

**MUST** hash passwords using bcrypt, argon2, or PBKDF2 with appropriate work factors (argon2id preferred).

#### 6.3 Security headers and transport
**MUST** use security headers middleware (e.g., Helmet for Express) to set:
- `Content-Security-Policy`
- `Strict-Transport-Security` (HSTS)
- `X-Content-Type-Options`
- `X-Frame-Options`
- `Referrer-Policy`

**MUST** enforce HTTPS in production environments.

#### 6.4 Rate limiting and DDoS protection
**MUST** implement rate limiting on public endpoints (e.g., $100$ requests per $15$ minutes per IP).

**SHOULD** implement stricter limits on authentication endpoints (e.g., $5$ attempts per $15$ minutes).

#### 6.5 Dependency security
**MUST** commit lockfiles (`package-lock.json`, `pnpm-lock.yaml`, `yarn.lock`) and use `npm ci` (or equivalent) in CI/CD.

**MUST** run vulnerability audits (`npm audit`) in CI/CD and fail builds on high-severity vulnerabilities unless explicitly waived with documented justification.

### 7. Performance optimization

#### 7.1 Profiling and monitoring
**MUST** measure before optimizing; use Node.js `--inspect`, Clinic.js, or `perf_hooks` for empirical analysis.

**SHOULD** implement health check endpoints (`/health`, `/ready`, `/live`) for orchestrators.

#### 7.2 Memory management
**MUST** use streams (`fs.createReadStream`, `pipeline`) for processing large files ($> \text{buffer size}$); never load multi-gigabyte files into memory.

**SHOULD** monitor memory usage (`process.memoryUsage()`) and set heap size limits appropriately for container environments.

#### 7.3 Caching strategies
**SHOULD** implement caching based on deployment topology:
- Single instance: In-memory LRU (limited scope)
- Multi-instance: External cache (Redis, Memcached) for shared state

**MUST** implement cache invalidation strategies; never cache user-specific sensitive data in shared caches without isolation.

#### 7.4 Database and I/O
**MUST** use connection pooling for database connections with configured limits matching container resources.

**SHOULD** set query timeouts to prevent runaway queries from consuming connections.

### 8. Testing and quality assurance

#### 8.1 Test structure
**MUST** organize tests in `tests/` directory or colocated as `*.test.ts`, following the Arrange-Act-Assert (AAA) pattern.

**MUST** ensure tests are deterministic: no reliance on wall-clock time (use fake timers), no shared mutable state between tests, no network calls in unit tests (mock at boundaries).

#### 8.2 Coverage and tooling
**MUST** use a single test runner per repository (Jest, Vitest, or Node.js native test runner).

**SHOULD** target $80\%$ line coverage for business logic; critical paths (authentication, financial calculations) **SHOULD** approach $100\%$.

**MUST** run tests, linting, and type checking in CI/CD before merging.

#### 8.3 Test categories
**MUST** include:
- **Unit tests**: Business logic in isolation (mocked dependencies)
- **Integration tests**: Database and external service interactions (use test containers or dedicated test databases)
- **Contract tests**: For external API dependencies (when applicable)

### 9. API design

#### 9.1 REST conventions
**MUST** use resource-oriented URLs with plural nouns and HTTP methods semantically:

| Method | URL | Action | Status |
|--------|-----|--------|--------|
| GET | `/resources` | List | 200 |
| GET | `/resources/:id` | Retrieve | 200 |
| POST | `/resources` | Create | 201 |
| PATCH | `/resources/:id` | Partial update | 200 |
| PUT | `/resources/:id` | Replace | 200 |
| DELETE | `/resources/:id` | Remove | 204 |

**MUST** use kebab-case for multi-word resources: `/order-items`.

**SHOULD** version APIs via URL path (`/v1/users`) or header (`Accept: application/vnd.api.v1+json`).

#### 9.2 Response envelopes
**MUST** return consistent JSON structures:

Success:
```json
{
  "data": { ... },
  "meta": { "timestamp": "2024-01-15T10:00:00Z", "requestId": "uuid" }
}
```

Error:
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input",
    "details": [{ "field": "email", "message": "Invalid format" }],
    "requestId": "uuid"
  }
}
```

#### 9.3 Documentation
**MUST** document APIs using OpenAPI $3.0+$ (Swagger) or GraphQL SDL, generated from code or maintained as the source of truth (not manually written docs).

### 10. Deployment and operations

#### 10.1 Containerization
**SHOULD** use multi-stage Docker builds (builder stage with dev dependencies, production stage with only production dependencies).

**MUST** run containers as non-root user (`USER node` or custom UID $≥ 1000$).

**MUST** use `.dockerignore` to exclude `node_modules`, `.env`, and source control files.

#### 10.2 Process management
**SHOULD** use the `cluster` module or PM2 only when not using container orchestrators (Kubernetes, ECS); prefer horizontal pod scaling over Node.js clustering in containerized environments.

**MUST** handle zombie processes and ensure proper signal handling in containerized environments.

#### 10.3 Database migrations
**MUST** manage schema changes via versioned migration files (using Knex, TypeORM migrations, or similar).

**MUST** apply migrations as a separate step before application startup in production, or as an init container.

### 11. Accessibility and multi-platform considerations

#### 11.1 Generated content accessibility
When Node.js generates HTML, emails, or PDFs (SSR):

**MUST** use semantic HTML elements (`<main>`, `<nav>`, `<article>`) over generic `<div>` where appropriate.

**MUST** include `alt` text for images, `label` associations for form inputs, and sufficient color contrast if generating styled content.

**SHOULD** ensure keyboard navigability for generated interactive elements.

#### 11.2 Cross-platform compatibility
**MUST** not rely on OS-specific behaviors (case-insensitive file paths, shell-specific commands) in runtime code.

**MUST** use path utilities (`node:path`, `node:path/posix`) instead of string concatenation for file paths.

---

## Appendices

### Appendix A: Application instructions

**When generating new code:**
1. Analyze requirements for architectural impact (security, data handling, scalability).
2. Produce minimal, complete implementations following the "MUST" standards.
3. Include:
   - Proper error handling with custom error classes
   - Input validation at boundaries
   - JSDoc comments for public APIs
   - Corresponding unit test stubs
4. State assumptions explicitly if repository context is unknown (e.g., "Assuming ESM based on current standards").

**When reviewing existing code:**
1. Output a **Compliance Report** with three sections:
   - **Critical**: MUST violations (security risks, error swallowing, hardcoded secrets)
   - **Major**: SHOULD violations (architecture, test coverage)
   - **Minor**: Style or documentation gaps
2. For each violation, provide:
   - Standard reference (e.g., "6.1 Input Validation")
   - Line number and problematic code snippet
   - Suggested fix using diff format
3. Calculate compliance score: $\frac{\text{Standards Passed}}{\text{Total Applicable Standards}} \times 100\%$

**Response formatting:**
- Use checklists for compliance status
- Use `diff` code blocks for suggested changes
- Bold **MUST**, **SHOULD**, and **MAY** references

### Appendix B: Enforcement checklist

Critical **MUST** items for quick validation:

- [ ] **Architecture**: Separation of concerns (delivery/domain/infra); feature or layer based organization; single entry point
- [ ] **Naming**: `kebab-case` files, `camelCase` functions, `PascalCase` classes
- [ ] **Modules**: ESM with `import`/`export`, `node:` specifiers for built-ins
- [ ] **Tooling**: ESLint + Prettier configured; no manual formatting debates
- [ ] **Config**: Environment variables only; no hardcoded secrets; validated at startup
- [ ] **Async**: `async/await` used; no callback hell; errors propagated, not swallowed
- [ ] **Errors**: Custom error classes defined; global handlers registered; graceful shutdown implemented
- [ ] **Security**: Input validated with schemas; parameterized queries; Helmet headers; rate limiting; dependency auditing
- [ ] **Performance**: CPU work offloaded to workers; streams for large files; connection pooling
- [ ] **Testing**: Deterministic tests; $80\%+$ coverage target; CI integration

### Appendix C: Sample configuration

#### `package.json` (excerpt)
```json
{
  "type": "module",
  "engines": {
    "node": ">=20.11.0"
  },
  "scripts": {
    "dev": "node --watch src/index.js",
    "build": "tsc",
    "start": "node dist/index.js",
    "lint": "eslint . --ext .ts,.js",
    "format": "prettier --write .",
    "test": "vitest run",
    "test:coverage": "vitest run --coverage",
    "typecheck": "tsc --noEmit"
  }
}
```

#### `eslint.config.js` (Flat config, ESM)
```javascript
import js from '@eslint/js';
import tseslint from 'typescript-eslint';
import prettier from 'eslint-config-prettier';

export default [
  js.configs.recommended,
  ...tseslint.configs.recommendedTypeChecked,
  {
    languageOptions: {
      parserOptions: {
        project: true,
        tsconfigRootDir: import.meta.dirname,
      },
    },
    rules: {
      'no-console': 'off',
      '@typescript-eslint/no-explicit-any': 'error',
    },
  },
  prettier,
];
```

#### `prettier.config.js`
```javascript
export default {
  semi: true,
  singleQuote: true,
  trailingComma: 'all',
  printWidth: 100,
  tabWidth: 2,
};
```

#### `tsconfig.json`
```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "NodeNext",
    "moduleResolution": "NodeNext",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "exactOptionalPropertyTypes": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "sourceMap": true,
    "declaration": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

#### `.env.example`
```bash
# Database
DATABASE_URL=postgresql://user:password@localhost:5432/myapp
DATABASE_POOL_SIZE=20

# Application
PORT=3000
NODE_ENV=development
LOG_LEVEL=info

# Security
JWT_SECRET=REPLACE_ME_IN_PRODUCTION
BCRYPT_ROUNDS=12
```

### Appendix D: Examples

#### D.1 Error handling (Non-compliant vs. Compliant)

**Non-compliant** (callback, no error handling, swallows errors)
```javascript
// server.js
app.get('/user/:id', (req, res) => {
  db.query('SELECT * FROM users WHERE id = ' + req.params.id, (err, data) => {
    if (err) {
      console.log(err);
      return res.status(500).send('Error');
    }
    res.json(data);
  });
});

process.on('uncaughtException', (err) => {
  console.error(err); // Continues running - dangerous
});
```

**Compliant** (async/await, validation, custom errors, centralized handling)
```typescript
// src/shared/errors.ts
export class AppError extends Error {
  constructor(
    message: string,
    public readonly code: string,
    public readonly statusCode: number,
    public readonly isOperational = true
  ) {
    super(message);
    Error.captureStackTrace(this, this.constructor);
  }
}

export class ValidationError extends AppError {
  constructor(message: string) {
    super(message, 'VALIDATION_ERROR', 400);
  }
}

export class NotFoundError extends AppError {
  constructor(resource: string, id: string) {
    super(`${resource} not found`, 'NOT_FOUND', 404);
  }
}

// src/modules/user/user.controller.ts
import { NextFunction, Request, Response } from 'express';
import { z } from 'zod';
import { userService } from './user.service.js';

const GetUserSchema = z.object({
  params: z.object({
    id: z.string().uuid(),
  }),
});

export const getUser = async (
  req: Request,
  res: Response,
  next: NextFunction
) => {
  try {
    const { id } = GetUserSchema.parse(req).params;
    const user = await userService.findById(id);
    
    if (!user) {
      throw new NotFoundError('User', id);
    }
    
    res.json({ data: user, meta: { timestamp: new Date().toISOString() } });
  } catch (error) {
    next(error);
  }
};

// src/shared/error-handler.ts
import { logger } from './logger.js';

export const errorHandler = (err: Error, req: Request, res: Response, _next: NextFunction) => {
  logger.error({ err, requestId: req.id }, 'Error processing request');
  
  if (err instanceof AppError && err.isOperational) {
    res.status(err.statusCode).json({
      error: {
        code: err.code,
        message: err.message,
        requestId: req.id,
      },
    });
    return;
  }
  
  // Programming error - don't leak details
  res.status(500).json({
    error: {
      code: 'INTERNAL_ERROR',
      message: 'An unexpected error occurred',
      requestId: req.id,
    },
  });
};

// src/index.ts
import { createServer } from 'node:http';
import { app } from './app.js';
import { logger } from './shared/logger.js';

const server = createServer(app);
const PORT = process.env.PORT || 3000;

server.listen(PORT, () => {
  logger.info(`Server listening on port ${PORT}`);
});

// Global error handlers
process.on('unhandledRejection', (reason) => {
  logger.fatal({ reason }, 'unhandledRejection');
  shutdown();
});

process.on('uncaughtException', (error) => {
  logger.fatal({ error }, 'uncaughtException');
  shutdown();
});

async function shutdown() {
  server.close(() => {
    logger.info('HTTP server closed');
    // Close DB connections here
    process.exit(1);
  });
  
  // Force exit after timeout
  setTimeout(() => {
    logger.error('Forced shutdown');
    process.exit(1);
  }, 30000);
}
```

#### D.2 Configuration validation (Compliant)

```typescript
// src/config/index.ts
import { z } from 'zod';

const envSchema = z.object({
  NODE_ENV: z.enum(['development', 'test', 'production']).default('development'),
  PORT: z.coerce.number().default(3000),
  DATABASE_URL: z.string().url(),
  JWT_SECRET: z.string().min(32),
  LOG_LEVEL: z.enum(['debug', 'info', 'warn', 'error']).default('info'),
});

const parseEnv = () => {
  const result = envSchema.safeParse(process.env);
  
  if (!result.success) {
    console.error('Invalid configuration:', result.error.format());
    process.exit(1);
  }
  
  return result.data;
};

export const config = parseEnv();
```

#### D.3 Security headers and input validation (Compliant)

```typescript
// app.ts
import express from 'express';
import helmet from 'helmet';
import rateLimit from 'express-rate-limit';
import { config } from './config/index.js';

const app = express();

// Security headers
app.use(helmet());

// Rate limiting
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // limit each IP to 100 requests per windowMs
  standardHeaders: true,
  legacyHeaders: false,
});
app.use('/api/', limiter);

// Stricter limit for auth
const authLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 5,
  skipSuccessfulRequests: true,
});
app.use('/api/auth/login', authLimiter);

// Body parsing with limits
app.use(express.json({ limit: '10kb' }));
app.use(express.urlencoded({ extended: true, limit: '10kb' }));
```
