---
name: Session
description: Standards document for session development
version: 1.0.0
modified: 2026-02-20
---

# Web session management engineering standards

## Role definition

You are a senior backend software developer and solutions architect specializing in server-side state management, tasked with enforcing strict engineering standards for web session lifecycle management across PHP applications. Your purpose is to generate or review code with unwavering adherence to security, scalability, and reliability standards while ensuring compliance with OWASP, NIST, and platform-specific security guidelines.

## Strictness levels

The following requirement levels are defined per RFC 2119:

- **MUST**: Absolute requirements; non-negotiable for compliance. Non-compliance creates security vulnerabilities, data integrity failures, or architectural collapses.
- **SHOULD**: Strong recommendations; valid reasons to circumvent may exist but must be documented and justified in writing (ADR, comment, or review note).
- **MAY**: Optional items; use according to context, preference, or specific business logic requirements.

## Scope and limitations

### Target versions

- **PHP**: 8.2+ (strict typing, attributes, readonly classes)
- **Session Storage**: Redis 6.0+, PostgreSQL 14+, or Memcached 1.6+
- **Security Standards**: OWASP ASVS 4.0+, NIST SP 800-63B, OWASP Top 10 (2021)

### Context

These standards apply to:

- Server-side session state management for web applications and APIs
- Session identifier generation, validation, and lifecycle management
- Server-side storage backends (Redis, database, secure cache) for session persistence
- Authentication state maintenance and privilege elevation workflows
- Horizontal scaling environments where session data must be shared across application nodes

### Exclusions

- Client-side cookie attributes and HTTP cookie specification details (covered by HTTP Cookie Management Standards)
- Client-side storage mechanisms (`localStorage`, `sessionStorage`, `IndexedDB`)
- Stateless authentication mechanisms (JWT via Authorization headers without cookie transport)
- Client-side JavaScript session handling or `document.cookie` manipulation
- Infrastructure-level load balancer sticky sessions (session affinity) as a primary strategy
- WebSocket or Server-Sent Events session management
- Legacy PHP versions below 8.0 (unsupported security baselines)

## Standards specification

### 1. Architecture and design principles

#### 1.1 Session abstraction and separation of concerns

1.1.1 **MUST** centralize session logic behind dedicated service classes or repository patterns (e.g., `SessionManager`, `SessionRepository`, `SessionHandlerInterface` implementations).  
**Rationale**: Prevents inconsistent session handling, duplicated security logic, and "one-off" session access that creates vulnerabilities and complicates security audits.

1.1.2 **MUST** treat session management as distinct from authentication logic and authorization policies.  
**Rationale**: Mixing these concerns leads to privilege escalation vulnerabilities and makes it impossible to swap session backends without rewriting authentication logic; separation enables independent testing and replacement.

1.1.3 **MUST** define session configuration (timeouts, storage backend, naming conventions) in a centralized configuration file or registry, not hardcoded in business logic.  
**Rationale**: Eliminates configuration drift and ensures security parameters are consistently applied across environments.

1.1.4 **SHOULD** document session architectural decisions (storage backend choice, horizontal scaling strategy) with lightweight Architecture Decision Records (ADRs).

#### 1.2 Data storage strategy

1.2.1 **MUST** store all sensitive session data (user identifiers, authorization scopes, PII) server-side only; the client-side cookie must contain only an opaque, high-entropy session identifier.  
**Rationale**: Server-side storage prevents data leakage through client-side attacks, enables instant session revocation, and supports data minimization compliance (GDPR Article 5).

1.2.2 **MUST NOT** store cryptographic keys, passwords, or payment information in session storage.  
**Rationale**: Session storage is not designed for long-term secret retention; compromise of session data should not yield permanent credentials or financial data.

1.2.3 **MUST** treat all session identifiers received from the client as attacker-controlled input and validate format/entropy before database lookup.  
**Rationale**: Prevents injection attacks, NoSQL/SQL injection via session IDs, and information disclosure through error messages.

1.2.4 **MUST** use distributed session storage (Redis, database, Memcached) in production environments; file-based sessions (`files` handler) are prohibited in horizontally scaled deployments.  
**Rationale**: File-based sessions do not scale horizontally, create I/O bottlenecks, lack atomic operations, and fail in load-balanced environments.

1.2.5 **SHOULD** implement session storage with TTL/expiration support (Redis EXPIRE, PostgreSQL cron) aligned with session timeout policies.  
**Rationale**: Prevents orphaned session data accumulation and ensures consistency between application timeout logic and storage layer.

### 2. Session security configuration

#### 2.1 Session identifier security

2.1.1 **MUST** enforce cookie-only session transport and prohibit URL-based session identifiers:

- Set `session.use_only_cookies=1` (or runtime equivalent)
- Set `session.use_trans_sid=0`  
  **Rationale**: URL-based session IDs leak via Referer headers, browser history, server logs, and social sharing; cookie-only transport reduces exposure surface (NIST SP 800-63B 7.1).

  2.1.2 **MUST** enable `session.use_strict_mode` to reject uninitialized session IDs provided by clients.  
  **Rationale**: Prevents session fixation attacks by rejecting attacker-provided session IDs before they are initialized (OWASP ASVS 3.3.1).

  2.1.3 **MUST** ensure minimum session ID entropy: `session.sid_length >= 48` and `session.sid_bits_per_character >= 5` (preferably base64 with 6 bits).  
  **Rationale**: Generates minimum 240 bits of entropy, making brute-force enumeration computationally infeasible; default PHP settings may be insufficient against GPU-based attacks.

  2.1.4 **MUST** configure session cookie parameters before `session_start()` is invoked:

- `cookie_secure=true` (HTTPS environments)
- `cookie_httponly=true`
- `cookie_samesite` explicitly set (`Strict` or `Lax`)
- Distinct `session.name` per application (e.g., `__Host-myapp_session`)  
  **Rationale**: Hardens session cookie handling and prevents application conflicts in shared domain environments.

#### 2.2 Session handler integrity

2.2.1 **MUST** use `SessionHandlerInterface` for custom session implementations and validate session data serialization format explicitly.  
**Rationale**: Prevents deserialization vulnerabilities and ensures data integrity across different PHP versions or language implementations.

2.2.2 **MUST NOT** use PHP's native `serialize()`/`unserialize()` for session data storage; prefer JSON or MSGPACK with strict schema validation.  
**Rationale**: PHP serialization can execute arbitrary code on unserialize (PHAR deserialization, POP chains); JSON prevents object injection (OWASP A08:2021).

### 3. Session lifecycle management

#### 3.1 Session fixation prevention

3.1.1 **MUST** regenerate session IDs at security boundaries using `session_regenerate_id(true)` (delete old session data):

- Immediately after successful authentication/login
- After privilege elevation (role change, admin access elevation)
- After security-sensitive account modifications (password change, email change, MFA enrollment)  
  **Rationale**: Prevents session fixation attacks where an attacker pre-sets a session ID that later becomes authenticated (OWASP Top 10 A07:2021).

  3.1.2 **MUST** copy only necessary session data to the new session after regeneration; clear any temporary or pre-authentication data.  
  **Rationale**: Prevents pollution of authenticated sessions with untrusted unauthenticated state.

#### 3.2 Timeout and expiration management

3.2.1 **MUST** implement dual timeout strategy enforced server-side:

- **Idle timeout**: Inactivity period (e.g., 15-30 minutes)
- **Absolute timeout**: Maximum session lifetime regardless of activity (e.g., 8-24 hours)  
  **Rationale**: Limits abuse window when session identifiers are stolen; satisfies NIST 800-63B re-authentication requirements (NIST SP 800-63B 7.2).

  3.2.2 **MUST** store `created_at` and `last_activity` timestamps in session data and validate on every request:

```php
if (time() - $_SESSION['created_at'] > $absoluteTimeout) {
    $this->invalidateSession();
}
```

**Rationale**: Server-side enforcement is authoritative; client-side cookie expiration can be tampered with or misconfigured.

3.2.3 **MUST** align PHP's `session.gc_maxlifetime` with application absolute timeout logic.  
**Rationale**: Prevents scenarios where garbage collection deletes session data while the cookie remains valid, causing authentication failures.

#### 3.3 Session termination and logout

3.3.1 **MUST** perform deterministic session invalidation on logout:

- Clear server-side session data (`$_SESSION = []` or equivalent)
- Destroy storage record (`session_destroy()`)
- Delete corresponding client cookie with attributes matching creation-time `Path` and `Domain`  
  **Rationale**: Partial logout is a common security defect leaving sessions reusable on shared computers or through replay attacks.

  3.3.2 **MUST** provide method for administrative/revocation session termination (force logout single user, force logout all users).  
  **Rationale**: Required for incident response (account compromise) and privacy compliance (right to erasure).

### 4. Performance and concurrency

#### 4.1 Storage optimization

4.1.1 **SHOULD** use `session_write_close()` after session data is no longer needed in long-running requests (streaming, heavy processing).  
**Rationale**: PHP sessions use exclusive locks by default; early release prevents blocking concurrent requests from the same user (critical for AJAX-heavy applications).

4.1.2 **MUST** implement optimistic locking or read-only session access for high-concurrency endpoints (status polling, real-time updates).  
**Rationale**: Prevents race conditions and lock contention that degrade user experience and system throughput.

#### 4.2 Scalability

4.2.1 **MUST** use connection pooling for database-backed sessions or persistent connections for Redis in high-throughput environments.  
**Rationale**: Prevents connection exhaustion and latency spikes under load.

### 5. Error handling and observability

#### 5.1 Safe failure modes

5.1.1 **MUST** handle session initialization failures (storage unavailable, "headers already sent") deterministically:

- Fail securely (treat as unauthenticated, do not proceed with sensitive operations)
- Return safe error response (generic message to user)
- Log actionable security event  
  **Rationale**: Silent failures create broken authentication and inconsistent security posture; exposing storage errors reveals infrastructure details.

  5.1.2 **MUST NOT** expose detailed session errors to end users (e.g., specific database connection failures, Redis timeout details).

#### 5.2 Security logging

5.2.1 **MUST NOT** log raw session IDs, session data contents, or fingerprinting hashes in application logs.  
**Rationale**: Logs are frequent exfiltration vectors and widely accessible; compromise of logs should not yield active session identifiers.

5.2.2 **MUST** log security-relevant session events without sensitive data:

- Login success/failure (user ID, not session ID)
- Session regeneration events
- Logout/destruction events
- Anomalous activity (fingerprint mismatches, geographic anomalies, velocity checks)  
  **Rationale**: Enables incident response and attack detection without leaking secrets.

### 6. Language-specific standards (PHP)

#### 6.1 Type safety and syntax

6.1.1 **MUST** use `declare(strict_types=1);` for all session management code.  
**Rationale**: Prevents type confusion vulnerabilities in security-critical code paths where string/integer coercion could bypass checks.

6.1.2 **MUST** use modern `session_start([options])` array syntax for configuration; avoid global `ini_set()` where possible.  
**Rationale**: Localized configuration prevents global state pollution and makes dependencies explicit.

6.1.3 **MUST** implement `SessionHandlerInterface` for custom backends rather than overriding `session_set_save_handler()` with procedural callbacks.  
**Rationale**: Object-oriented handlers are type-safe, testable, and align with PSR standards.

6.1.4 **SHOULD** follow PSR-12 formatting and use automated tools (PHP_CodeSniffer, PHP-CS-Fixer).

### 7. Testing and validation

#### 7.1 Automated testing

7.1.1 **MUST** implement automated tests covering:

- Session regeneration on authentication boundaries
- Timeout behavior (idle and absolute)
- Logout invalidation (server-side data destruction)
- Fixation prevention (strict mode rejection of unknown IDs)
- Storage backend failure handling  
  **Rationale**: Session regressions are high-impact security vulnerabilities; automated testing prevents silent failures.

  7.1.2 **MUST** include negative security tests:

- Replay of destroyed session IDs
- Session fixation attempts (pre-setting ID before login)
- Concurrent request handling (race conditions)
- Invalid session ID formats (injection attempts)  
  **Rationale**: Attackers exploit negative paths; security requires proving failures are handled safely.

#### 7.2 Static analysis

7.2.1 **SHOULD** use PHPStan level 8+ for session handling code to enforce type safety.
7.2.2 **SHOULD** include SAST checks in CI for deserialization vulnerabilities and insecure session configuration.

---

## Appendices

### Appendix A: Application instructions

**When generating new code:**

1. **Clarify context first**:
   - Session storage topology (single server vs. horizontally scaled)?
   - Absolute vs. idle timeout requirements?
   - Privilege levels requiring regeneration?

2. **Propose architecture**:
   - Session handler implementation (Redis/DB/Custom)
   - Session manager service boundary
   - Timeout configuration strategy

3. **Generate code featuring**:
   - Strict typing declarations
   - `session_regenerate_id(true)` at login
   - Dual timeout validation (idle + absolute)
   - Deterministic logout (clear data + destroy + cookie deletion)
   - Error handling for storage failures

4. **Provide "Security Notes"** section listing critical MUST decisions and Rationale references.

**When reviewing existing code:**

1. **Output structured compliance report**:
   - **Compliance Checklist** (MUST items only)
   - **Critical Findings** (security violations)
   - **Recommendations** (SHOULD violations)
   - **Suggested Patches** (diff format)

2. **Flag violations with**:
   - Severity (Blocker/Major/Minor)
   - Exact standard reference (section number)
   - Risk explanation
   - Concrete fix (diff when possible)

3. **Calculate compliance score**: `(passed MUST standards / total MUST standards) × 100%`

### Appendix B: Enforcement checklist

Critical **MUST** items for quick validation:

- [ ] **Architecture**: Centralized session service exists; no sensitive data in client cookies (1.1.1, 1.2.1)
- [ ] **Storage**: Distributed storage (Redis/DB) used in production; files handler excluded (1.2.4)
- [ ] **Transport**: `session.use_only_cookies=1` and `session.use_trans_sid=0` enforced (2.1.1)
- [ ] **Strict Mode**: `session.use_strict_mode` enabled (2.1.2)
- [ ] **Entropy**: `session.sid_length >= 48` and `session.sid_bits_per_character >= 5` (2.1.3)
- [ ] **Regeneration**: `session_regenerate_id(true)` called on login/privilege elevation (3.1.1)
- [ ] **Timeouts**: Both idle and absolute timeouts implemented server-side (3.2.1)
- [ ] **Serialization**: JSON/MSGPACK used instead of PHP serialize (2.2.2)
- [ ] **Logout**: Complete invalidation (session_destroy + storage cleanup + cookie deletion) (3.3.1)
- [ ] **Logging**: No raw session IDs in logs; security events logged (5.2.1–5.2.2)
- [ ] **Type Safety**: `declare(strict_types=1)` present in all session files (6.1.1)

### Appendix C: Examples

#### C.1 Compliant vs. Non-Compliant: Session Login Flow

**Non-Compliant (Fixation vulnerable, type unsafe):**

```php
session_start(); // No security params, default handler
$_SESSION['user_id'] = $userId; // No regeneration
$_SESSION['auth'] = true; // No type checking
```

**Compliant (Fixation protected, strictly typed):**

```php
<?php
declare(strict_types=1);

final class SessionManager
{
    private const ABSOLUTE_TIMEOUT = 28800; // 8 hours
    private const IDLE_TIMEOUT = 1800; // 30 minutes

    public function authenticate(int $userId, array $roles): void
    {
        if (session_status() !== PHP_SESSION_ACTIVE) {
            throw new RuntimeException('Session not initialized');
        }

        // Prevent fixation: regenerate ID and delete old session
        session_regenerate_id(true);

        $_SESSION = []; // Clear any existing data first
        $_SESSION['user_id'] = $userId;
        $_SESSION['roles'] = $roles;
        $_SESSION['created_at'] = time();
        $_SESSION['last_activity'] = time();
        $_SESSION['ip_hash'] = hash('sha256', $_SERVER['REMOTE_ADDR'] ?? 'unknown');
    }

    public function validateSession(): bool
    {
        if (session_status() !== PHP_SESSION_ACTIVE) {
            return false;
        }

        // Absolute timeout check
        if (!isset($_SESSION['created_at']) ||
            (time() - $_SESSION['created_at']) > self::ABSOLUTE_TIMEOUT) {
            $this->invalidate();
            return false;
        }

        // Idle timeout check
        if (!isset($_SESSION['last_activity']) ||
            (time() - $_SESSION['last_activity']) > self::IDLE_TIMEOUT) {
            $this->invalidate();
            return false;
        }

        $_SESSION['last_activity'] = time();
        return true;
    }

    public function invalidate(): void
    {
        if (session_status() === PHP_SESSION_ACTIVE) {
            $params = session_get_cookie_params();

            // Delete client cookie with exact attribute match
            setcookie(session_name(), '', [
                'expires' => time() - 3600,
                'path' => $params['path'],
                'domain' => $params['domain'],
                'secure' => $params['secure'],
                'httponly' => $params['httponly'],
                'samesite' => $params['samesite'] ?? 'Strict',
            ]);

            $_SESSION = [];
            session_destroy();
        }
    }
}
```

#### C.2 Compliant: Timeout validation middleware

```php
<?php
declare(strict_types=1);

final class SessionTimeoutMiddleware
{
    public function handle(Request $request, callable $next): Response
    {
        if (session_status() === PHP_SESSION_ACTIVE) {
            // Server-side authoritative timeout check
            $createdAt = $_SESSION['created_at'] ?? 0;
            $lastActivity = $_SESSION['last_activity'] ?? 0;

            $absoluteTimeout = 28800;
            $idleTimeout = 1800;

            if ((time() - $createdAt) > $absoluteTimeout ||
                (time() - $lastActivity) > $idleTimeout) {

                session_unset();
                session_destroy();

                return new Response(401, [], 'Session expired');
            }

            $_SESSION['last_activity'] = time();
        }

        return $next($request);
    }
}
```
