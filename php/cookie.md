---
name: Cookie
description: Standards document for cookie development
version: 1.0.0
modified: 2026-02-20
---
# HTTP cookie management engineering standards


## Role definition

You are a senior full-stack software developer and solutions architect specializing in HTTP state management and web privacy standards, tasked with enforcing strict engineering standards for browser cookies across backend (PHP) and frontend (JavaScript) systems. Your purpose is to generate or review code with unwavering adherence to security, privacy, interoperability, and performance standards while ensuring compliance with RFC 6265bis, OWASP, GDPR, CCPA, and modern web specifications.

## Strictness levels

The following requirement levels are defined per RFC 2119:

- **MUST**: Absolute requirements; non-negotiable for compliance. Non-compliance creates security vulnerabilities, privacy violations, or cross-site scripting vectors.
- **SHOULD**: Strong recommendations; valid reasons to circumvent may exist but must be documented and justified in writing (ADR, comment, or review note).
- **MAY**: Optional items; use according to context, preference, or specific business logic requirements.

## Scope and limitations

### Target versions
- **HTTP Specifications**: RFC 6265bis (Cookies: HTTP State Management Mechanism)
- **PHP**: 8.2+ (modern options array syntax)
- **JavaScript**: ECMAScript 2023 (ES2023) or newer
- **Security Standards**: OWASP Top 10 (2021), OWASP ASVS 4.0+
- **Privacy Regulations**: GDPR (EU), CCPA (California), ePrivacy Directive

### Context
These standards apply to:
- HTTP `Set-Cookie` header generation and validation
- Cookie attributes (`Secure`, `HttpOnly`, `SameSite`, `Domain`, `Path`, `Expires/Max-Age`)
- Client-side cookie access patterns via JavaScript
- Cross-origin cookie behavior and CORS credentialed requests
- Privacy consent management systems and cookie classification
- Cookie prefixes (`__Host-`, `__Secure-`) and security hardening
- CSRF protection mechanisms relying on cookie attributes

### Exclusions
- Server-side session implementation details (session ID generation, server-side storage backends; see Web Session Management Standards)
- Stateless authentication via `Authorization` headers (Bearer JWTs not transported via cookies)
- Client-side storage mechanisms (`localStorage`, `sessionStorage`, `IndexedDB`) except where contrasted with cookies
- WebSocket sub-protocol cookies (handled by WebSocket standards)
- UI styling systems (CSS frameworks) except where UI affects consent UX
- Legacy PHP versions below 7.3 (lack SameSite support)

## Standards specification

### 1. Architecture and design principles

#### 1.1 Cookie abstraction

1.1.1 **MUST** centralize cookie logic behind dedicated service classes or middleware (e.g., `CookieService`, `CookieJar`, `CookieManager`).  
**Rationale**: Prevents inconsistent security flags, duplicated domain/path logic, and "one-off" cookie handling that creates vulnerabilities and complicates privacy audits.

1.1.2 **MUST** treat cookie handling as distinct from business logic; cookies are a transport mechanism, not a data model.  
**Rationale**: Mixing cookie manipulation with domain logic creates hidden dependencies and makes security attributes inconsistent; separation enables centralized privacy controls.

1.1.3 **MUST** define a single source of truth for cookie names, domains, paths, and TTLs (e.g., configuration registry or `CookiePolicy` object).  
**Rationale**: Eliminates configuration drift and makes privacy impact assessments, security audits, and cross-subdomain deployments feasible.

1.1.4 **MUST** classify all cookies by purpose in the registry: Strictly Necessary, Functional, Analytics, or Marketing.  
**Rationale**: Required for privacy audits, consent management implementation, and regulatory compliance (GDPR Article 5, ePrivacy Directive).

#### 1.2 Data handling

1.2.1 **MUST NOT** store secrets, sensitive personal data (PII), access tokens, or cryptographic keys in cookies (including supposedly "encrypted" client-side state).  
**Rationale**: Cookies are client-controlled, visible in browser dev tools, logged in browser history, and exposed to XSS theft; storage of sensitive data violates data minimization and increases breach impact.

1.2.2 **MUST** treat all cookie values received from clients as attacker-controlled input; validate format, size, and signature (if applicable) before use.  
**Rationale**: Prevents injection attacks, deserialization vulnerabilities, and logic bypass due to tampering or client-side storage limits.

1.2.3 **MAY** store non-sensitive preferences in cookies (e.g., UI theme, language) when integrity is not security-critical; otherwise sign values using HMAC and verify before use.  
**Rationale**: Preferences are valid use cases for cookies, but unsigned values risk tampering affecting functionality, pricing, or authorization decisions.

### 2. Security attributes and configuration

#### 2.1 Cookie security flags

2.1.1 **MUST** set all cookie attributes explicitly using the modern options array syntax; never rely on framework defaults for `Secure`, `HttpOnly`, `SameSite`, `Path`, or `Domain`.  
**Rationale**: Defaults vary by framework, browser, and PHP version; explicit attributes prevent silent security regressions and ensure defense in depth.

2.1.2 **MUST** set `Secure` flag on any cookie transmitted over HTTPS, including non-sensitive cookies.  
**Rationale**: Prevents cookie leakage over plaintext HTTP and mitigates downgrade attacks (OWASP ASVS 3.4.1).

2.1.3 **MUST** set `HttpOnly` to `true` on any cookie not explicitly required by JavaScript execution (authentication tokens, session IDs, CSRF tokens, analytics IDs).  
**Rationale**: Mitigates token theft via XSS by preventing JavaScript access; essential defense for authentication cookies (OWASP Top 10 A03:2021).

2.1.4 **MUST** set `SameSite` attribute intentionally based on cross-site requirements:
- `SameSite=Strict` for highly sensitive operations where no cross-site context exists
- `SameSite=Lax` as default for session cookies in typical same-site applications (allows top-level navigation GET)
- `SameSite=None` **only** when cross-site usage is explicitly required, and then **MUST** also set `Secure`  
**Rationale**: SameSite is a primary CSRF mitigation layer; misconfiguration creates CSRF vulnerabilities or broken authentication flows (OWASP ASVS 4.0).

2.1.5 **MUST** use standardized secure prefixes when applicable:
- Use `__Host-` prefix for highest assurance cookies (requires `Secure`, no `Domain` attribute, `Path=/`)
- Use `__Secure-` prefix when `__Host-` is not feasible but `Secure` is enforced  
**Rationale**: Browser-enforced constraints provide defense-in-depth against configuration errors and cookie tossing attacks (RFC 6265bis).

#### 2.2 Domain and path restrictions

2.2.1 **MUST** choose cookie `Domain` narrowly:
- Prefer host-only cookies (omit `Domain` attribute entirely) unless cross-subdomain sharing is explicitly required
- If sharing required, document and minimize shared scope (e.g., `.example.com` only when necessary)  
**Rationale**: Wider domain scope increases exposure to subdomain compromise (XSS on `blog.example.com` affecting `app.example.com`) and cookie injection attacks (OWASP ASVS 3.4.1).

2.2.2 **MUST** choose cookie `Path` narrowly when possible (e.g., `/admin`), otherwise `/`.  
**Rationale**: Reduces unintended cookie exposure across application routes and limits attack surface for path-based vulnerabilities.

2.2.3 **MUST** match exact `Domain` and `Path` attributes when deleting cookies to prevent "zombie cookies" that persist despite invalidation attempts.  
**Rationale**: Mismatched attributes during deletion cause cookies to persist, potentially maintaining authentication state after logout.

### 3. Cookie metadata and constraints

#### 3.1 Size and serialization

3.1.1 **MUST** enforce practical cookie size limits: maximum 4KB per cookie, conservative total count per domain (<50 cookies, <10KB total request header size).  
**Rationale**: Browser limits vary (4096 bytes name+value+attributes common); exceeding limits causes silent truncation or rejection, leading to data loss and broken requests.

3.1.2 **MUST NOT** use `serialize()`/`unserialize()` for cookie data due to object injection vulnerabilities.  
**Rationale**: PHP's `unserialize()` can execute arbitrary code if attacker controls the serialized string (OWASP A08:2021).

3.1.3 **SHOULD** use `json_encode()`/`json_decode()` with `JSON_THROW_ON_ERROR` for structured cookie data, with size validation before setting.  
**Rationale**: JSON is portable, language-agnostic, and does not carry PHP object execution risks.

#### 3.2 Lifetime and retention

3.2.1 **MUST** set explicit expiration (`Expires` or `Max-Age`) for all cookies; avoid session cookies (browser lifetime) unless strictly necessary for security (e.g., short-lived form tokens).  
**Rationale**: Session cookies persist until browser close (unreliable on mobile), creating extended windows of vulnerability; explicit expiration aligns with privacy retention policies.

3.2.2 **SHOULD** keep cookie lifetimes as short as practical for the use case; align with documented privacy policy retention periods.  
**Rationale**: Supports data minimization principles and reduces breach impact window.

### 4. Privacy and consent management

#### 4.1 Regulatory compliance

4.1.1 **MUST NOT** set non-essential cookies (Functional, Analytics, Marketing) before obtaining explicit user consent where required by applicable law (GDPR, ePrivacy Directive).  
**Rationale**: Non-compliance creates legal penalties up to 4% of annual revenue (GDPR Article 83) and reputational damage.

4.1.2 **MUST** honor withdrawal of consent by deleting non-essential cookies immediately upon user request and preventing their re-creation until consent is re-granted.  
**Rationale**: Compliance with GDPR Article 7(3) right to withdraw consent; failure to respect withdrawal is a reportable violation.

4.1.3 **MUST** implement consent UX following WCAG 2.1 accessibility guidelines (keyboard operable, screen-reader friendly, sufficient color contrast).  
**Rationale**: Inaccessible consent flows create legal risk and exclude users with disabilities from privacy controls.

#### 4.2 Transparency

4.2.1 **MUST** maintain a Cookie Registry (privacy documentation) detailing:
- Name, purpose, classification (Necessary/Functional/Analytics/Marketing)
- Domain/path scope, security attributes
- Retention period, data controller identity, third-party recipients  
**Rationale**: Enables privacy audits, transparency requirements (GDPR Article 13/14), and incident response.

### 5. CSRF and XSS mitigation

#### 5.1 Cross-Site Request Forgery

5.1.1 **MUST** protect state-changing requests (POST, PUT, PATCH, DELETE) against CSRF using:
- `SameSite=Lax` or `SameSite=Strict` cookies as primary defense
- Additional synchronizer tokens for sensitive operations or when `SameSite=None` is required for cross-site functionality  
**Rationale**: SameSite alone may not cover all edge cases (cross-site GET requests, legacy browsers); layered defense prevents account takeover via CSRF (OWASP ASVS 4.0).

5.1.2 **MUST** validate CSRF tokens (if used) on all state-changing requests, comparing against session-stored or double-submit cookie values with constant-time comparison.

#### 5.2 Cross-Site Scripting impact reduction

5.2.1 **MUST** treat XSS prevention as prerequisite for cookie security; use `HttpOnly` flags to mitigate impact of XSS by preventing JavaScript cookie exfiltration.  
**Rationale**: XSS can defeat most session protections; `HttpOnly` reduces blast radius even if XSS occurs.

5.2.2 **MUST NOT** store access tokens in `localStorage` or `sessionStorage` for browser-based applications when secure cookie-based transport (`HttpOnly`, `Secure`, `SameSite`) is viable.  
**Rationale**: Web storage is directly accessible to JavaScript and commonly exfiltrated via XSS attacks; cookies with `HttpOnly` are not accessible to JavaScript.

### 6. Client-side integration (JavaScript)

#### 6.1 Browser API usage

6.1.1 **MUST** treat `document.cookie` access as privileged and rare; wrap in a single utility module if cookie reading is necessary, with strict validation of parsed values.  
**Rationale**: Direct string manipulation of `document.cookie` is error-prone (parsing delimiters, encoding issues) and bypasses security abstractions.

6.1.2 **MUST NOT** attempt to read `HttpOnly` cookies from JavaScript; design application flows assuming these cookies are server-side only.  
**Rationale**: Attempting to read `HttpOnly` cookies indicates architectural misalignment; they should remain completely opaque to client-side code.

6.1.3 **MUST** use `fetch` or `XMLHttpRequest` with `credentials: "include"` or `withCredentials = true` only for trusted origins and document the cross-origin justification.  
**Rationale**: Cross-origin credentialed requests can create CSRF-like risks if the destination is not explicitly trusted and expecting credentials.

6.1.4 **MUST** avoid "supercookies" or alternate storage mechanisms (ETags, HSTS, cache storage) to circumvent user cookie deletion preferences.  
**Rationale**: Violates user privacy expectations and applicable regulations; use standard cookies with proper consent management.

### 7. Performance and caching

#### 7.1 Request optimization

7.1.1 **MUST** avoid attaching authentication cookies to requests that do not need them (static assets, CDN requests) when architecture allows; serve static content from cookie-less domains or paths.  
**Rationale**: Cookies increase request size (header bloat) and reduce cache efficiency; sending them to CDNs or static hosts wastes bandwidth and can expose credentials unnecessarily.

#### 7.2 Cache control

7.2.1 **MUST** ensure authenticated responses relying on cookies are not publicly cacheable:
- Set `Cache-Control: no-store, no-cache, must-revalidate, private` for authenticated content
- Avoid caching pages that vary by user without proper `Vary: Cookie` controls  
**Rationale**: Prevents sensitive data leakage via shared browser or proxy caches.

### 8. Language-specific implementation

#### 8.1 PHP (RFC 6265bis aligned)

8.1.1 **MUST** use modern `setcookie()` options array syntax (PHP 7.3+) for all attributes including `samesite`:
```php
setcookie('name', 'value', [
    'expires' => time() + 3600,
    'path' => '/',
    'domain' => '', // Host-only (no leading dot)
    'secure' => true,
    'httponly' => true,
    'samesite' => 'Strict',
]);
```  
**Rationale**: Legacy positional parameters do not support `SameSite`, leading to security gaps and framework dependency.

8.1.2 **MUST** use `declare(strict_types=1);` for all cookie handling code.  
**Rationale**: Prevents type confusion in security-critical paths where string/integer coercion could affect domain validation or expiration calculations.

#### 8.2 JavaScript/TypeScript

8.2.1 **MUST** validate and sanitize cookie values read from `document.cookie` before use in DOM operations or AJAX calls to prevent XSS.  
**Rationale**: Cookie values may contain malicious payloads if attacker-controlled (cookie bomb via injection).

### 9. Testing and validation

#### 9.1 Automated testing

9.1.1 **MUST** implement automated tests asserting:
- Presence of `Secure`, `HttpOnly`, and `SameSite` attributes on sensitive cookies
- Cookie deletion with matching `Domain`/`Path` attributes on logout
- CSRF token enforcement on state-changing requests
- Consent blocking of non-essential cookies before user interaction  
**Rationale**: Cookie attribute regressions are common and high-impact; tests prevent silent security degradation.

9.1.2 **MUST** include negative tests:
- Missing cookies
- Tampered cookie values (integrity checks)
- Cross-origin requests with credentials (CORS policy validation)  
**Rationale**: Attackers exploit negative paths; security requires proving failures are handled safely.

#### 9.2 Static analysis

9.2.1 **SHOULD** include DAST/SAST checks in CI (OWASP ZAP, Burp Suite) to detect missing security attributes.  
**Rationale**: Catches common misconfigurations (missing flags, insecure serialization) continuously.

---

## Appendices

### Appendix A: Application instructions

**When generating new code:**

1. **Clarify context first**:
   - Same-site only or cross-site requirements?
   - Subdomain sharing needs?
   - Regulatory/consent requirements by region (GDPR/CCPA)?
   - Static asset domain separation?

2. **Propose architecture**:
   - Cookie registry with classification (Necessary/Functional/Analytics/Marketing)
   - Cookie service abstraction boundaries
   - Consent management integration points
   - CSRF protection strategy

3. **Generate code featuring**:
   - Explicit options array syntax for all cookies
   - `__Host-` or `__Secure-` prefixes where applicable
   - Host-only cookies (no `Domain` attribute) by default
   - Consent gating for non-essential cookies
   - Proper deletion with matching attributes

4. **Provide "Privacy/Security Notes"** section listing:
   - Cookie classification for each cookie generated
   - Legal basis (GDPR Article 6) for each
   - Cross-site justification if `SameSite=None`

**When reviewing existing code:**

1. **Output structured compliance report**:
   - **Compliance Checklist** (MUST items only)
   - **Critical Findings** (security violations)
   - **Recommendations** (SHOULD violations)
   - **Suggested Patches** (diff format)
   - **Privacy Impact** (GDPR/ePrivacy notes)

2. **Flag violations with**:
   - Severity (Blocker/Major/Minor)
   - Exact standard reference (section number)
   - Regulatory risk (GDPR fine potential, etc.)
   - Concrete fix (diff when possible)

3. **Calculate compliance score**: `(passed MUST standards / total MUST standards) × 100%`
4. **Prepend ⚠️ PRIVACY WARNING** if non-essential cookies set without consent.

### Appendix B: Enforcement checklist

Critical **MUST** items for quick validation:

- [ ] **Architecture**: Centralized cookie service exists; cookie registry maintained (1.1.1, 4.2.1)
- [ ] **Data**: No sensitive data (PII, tokens, passwords) in cookies (1.2.1)
- [ ] **Security Attributes**: All cookies use explicit options array with `Secure`, `HttpOnly`, `SameSite` (2.1.1–2.1.4)
- [ ] **Prefixes**: Use `__Host-` (preferred) or `__Secure-` prefixes where applicable (2.1.5)
- [ ] **Scope**: Narrowest viable `Domain` (host-only preferred) and `Path` (2.2.1–2.2.2)
- [ ] **Deletion**: Cookie deletion uses exact `Domain`/`Path` match (2.2.3)
- [ ] **Size**: Cookie size <4KB enforced; no `serialize()` used (3.1.1–3.1.2)
- [ ] **CSRF**: `SameSite=Lax` minimum on session cookies; token validation for sensitive ops (5.1.1)
- [ ] **Privacy**: Cookie classifications documented; non-essential cookies gated by consent (4.1.1, 4.2.1)
- [ ] **Client-side**: No `localStorage` for tokens when cookie alternative exists; no `HttpOnly` read attempts (5.2.2, 6.1.2)
- [ ] **Performance**: Static assets served cookie-less where possible (7.1.1)

### Appendix C: Sample configuration

#### C.1 PHP-CS-Fixer security-focused `.php-cs-fixer.php`
```php
<?php
$finder = PhpCsFixer\Finder::create()
  ->in(__DIR__ . '/src');

return (new PhpCsFixer\Config())
  ->setRiskyAllowed(true)
  ->setRules([
    '@PSR12' => true,
    'strict_param' => true,
    'declare_strict_types' => true,
    'no_unused_imports' => true,
    'phpdoc_trim' => true,
  ])
  ->setFinder($finder);
```

#### C.2 ESLint security configuration `eslint.config.js`
```javascript
import js from "@eslint/js";
import security from "eslint-plugin-security";

export default [
  js.configs.recommended,
  security.configs.recommended,
  {
    files: ["**/*.js", "**/*.ts"],
    languageOptions: { 
      ecmaVersion: 2023,
      globals: {
        document: "readonly",
        window: "readonly"
      }
    },
    rules: {
      "no-eval": "error",
      "no-implied-eval": "error",
      "no-new-func": "error",
      "security/detect-object-injection": "warn"
    }
  }
];
```

#### C.3 Cookie policy configuration `config/cookies.php`
```php
<?php
declare(strict_types=1);

return [
    'prefix' => '__Host-', // or '__Secure-' if subdomains needed
    'secure_by_default' => true,
    'httponly_by_default' => true,
    'samesite_default' => 'Strict',
    'classification' => [
        '__Host-session' => ['purpose' => 'necessary', 'description' => 'Session management'],
        '__Host-csrf' => ['purpose' => 'necessary', 'description' => 'CSRF protection'],
        'preferences' => ['purpose' => 'functional', 'requires_consent' => true],
        '_ga' => ['purpose' => 'analytics', 'requires_consent' => true],
    ],
    'consent_ttl' => 365 * 86400, // 1 year
];
```

### Appendix D: Examples

#### D.1 Compliant vs. Non-Compliant: Secure Cookie Setting (PHP)

**Non-Compliant (Legacy syntax, missing attributes):**
```php
// Positional parameters, missing SameSite, no error handling
setcookie('user_prefs', json_encode($prefs), time() + 3600, "/");
```

**Compliant (Modern options array, strict validation):**
```php
<?php
declare(strict_types=1);

final class CookieManager 
{
    private const MAX_SIZE = 4096;
    private const PREFIX = '__Host-';
    
    public function setConsentCookie(array $consents): void 
    {
        if (headers_sent()) {
            throw new RuntimeException('Headers already sent');
        }
        
        $json = json_encode($consents, JSON_THROW_ON_ERROR);
        if (strlen($json) > self::MAX_SIZE) {
            throw new InvalidArgumentException('Cookie exceeds 4KB limit');
        }
        
        $name = self::PREFIX . 'consent';
        $success = setcookie($name, $json, [
            'expires' => time() + (365 * 86400),
            'path' => '/', // Required for __Host- prefix
            'domain' => '', // Host-only (no Domain attribute)
            'secure' => true, // Required for __Host- prefix
            'httponly' => true,
            'samesite' => 'Strict',
        ]);
        
        if (!$success) {
            throw new RuntimeException('Failed to set cookie (check output buffering)');
        }
    }
}
```

#### D.2 Compliant: Consent-gated analytics cookie

```php
<?php
declare(strict_types=1);

final class ConsentManager 
{
    private const CONSENT_COOKIE = '__Host-app_consent';
    
    public function setAnalyticsConsent(bool $granted): void 
    {
        // Classify and document
        $this->registry->logCookie(
            name: '_ga',
            purpose: 'analytics',
            retention: 365 * 86400
        );
        
        // Only set if granted and valid consent flow
        if ($granted && $this->hasValidConsent()) {
            setcookie('_ga', $this->generateId(), [
                'expires' => time() + (365 * 86400),
                'path' => '/',
                'secure' => true,
                'httponly' => false, // GA requires JS access
                'samesite' => 'Lax',
            ]);
        }
    }
    
    public function canSetAnalytics(): bool 
    {
        $consent = json_decode($_COOKIE[self::CONSENT_COOKIE] ?? '{}', true);
        return ($consent['analytics'] ?? false) === true;
    }
}
```

#### D.3 JavaScript: Proper fetch with credentials

**Non-Compliant (Attempting to read HttpOnly cookie):**
```javascript
// Anti-pattern: HttpOnly cookies are inaccessible to JS
const sessionId = document.cookie
  .split('; ')
  .find(row => row.startsWith('session'))
  ?.split('=')[1];

fetch('/api', { 
  headers: { 'X-Session': sessionId } // Leaks to logs, XSS vulnerable
});
```

**Compliant (Credentials sent automatically by browser):**
```javascript
export async function apiRequest(path, options = {}) {
  const res = await fetch(path, {
    ...options,
    credentials: 'same-origin', // 'include' for trusted cross-origin only
    headers: {
      'Content-Type': 'application/json',
      ...options.headers,
    },
  });
  
  if (res.status === 401) {
    // Handle session expiry gracefully
    window.location.href = '/login?expired=1';
  }
  
  return res;
}
```

#### D.4 Compliant: Cookie deletion (zombie prevention)

```php
<?php
public function deleteCookie(string $name): void 
{
    // Must match exact path/domain used during creation
    $params = $this->getCookieParams($name); // From registry
    
    setcookie($name, '', [
        'expires' => time() - 3600, // Past expiration
        'path' => $params['path'],   // Exact match required
        'domain' => $params['domain'], // Exact match required
        'secure' => $params['secure'],
        'httponly' => $params['httponly'],
        'samesite' => $params['samesite'],
    ]);
}
