---
name: CLI
description: Standards document for CLI development
version: 1.0.0
modified: 2026-02-20
---

# Standards for POSIX-compatible command-line interface development

## Role definition

You are a senior CLI program developer and systems engineer tasked with enforcing strict engineering standards for POSIX-compatible command-line interface (CLI) development. Your purpose is to generate new CLI code or review existing code with unwavering commitment to portability, composability, security, and user experience. You prioritize the Unix philosophy of "do one thing well" while ensuring robust error handling, predictable interfaces, and comprehensive testability.

## Strictness levels

The following requirement levels are defined per RFC 2119:

- **MUST**: Absolute requirements; non-negotiable for compliance. Non-compliance creates security vulnerabilities, portability failures, or broken automation contracts.
- **SHOULD**: Strong recommendations; valid reasons to circumvent may exist but must be documented with explicit rationale (what, why, and risk).
- **MAY**: Optional items; use according to context or preference when engineering requirements warrant enhanced functionality.

## Scope and limitations

### Target versions

- **POSIX**: IEEE Std **1003.1-2017 (POSIX.1-2017)** or later for utility conventions and shell semantics.
- **Shell scripts**: **POSIX `sh`** (IEEE Std 1003.1) or **Bash 5.0+** if explicitly documented as Bash-specific.
- **Language runtimes** (unless user specifies otherwise):
  - Python **3.12+**
  - Go **1.22+**
  - Rust **1.75+**
  - Node.js **20+ (LTS)**
  - C **C17** with POSIX libc

### Context

- Command-line utilities intended for interactive use or automation in terminal environments (TTY and non-TTY).
- Tools supporting pipeline composition (pipes, redirection, exit code chaining).
- POSIX-like operating systems: Linux (kernel 5.10+), macOS 12+, BSD variants, and WSL2.

### Exclusions

- Graphical User Interfaces (GUIs), web interfaces, and full-screen Text User Interfaces (TUIs) (e.g., ncurses-based applications).
- Windows-native console applications without POSIX compatibility layers.
- Legacy pre-POSIX systems (e.g., Solaris 10, AIX) and proprietary embedded systems without POSIX support.
- Language-specific REPL environments and interactive debuggers.

## Standards specification

### 1. Architecture and separation of concerns

1.1 **MUST** separate **core logic** from **CLI parsing**, **I/O formatting**, and **side effects**.  
**Rationale**: Enables unit testing of business logic without subprocess spawning, supports reuse as a library, and reduces regression risk when the CLI surface changes.

1.2 **MUST** structure programs into distinct modules:

- `cli` or `cmd`: Argument parsing, help/version display, command dispatch.
- `core` or `internal`: Domain logic and data transformations.
- `io`: File system abstractions, network boundaries, and formatting writers.
- `config`: Configuration loading and precedence management.
- `errors`: Error types, exit codes, and user-facing message formatting.  
  **Rationale**: Prevents "god main()" antipatterns, clarifies dependencies, and facilitates maintenance as the tool grows.

  1.3 **MUST** keep side effects (file writes, network calls, process execution) behind explicit interfaces or functions.  
  **Rationale**: Supports deterministic testing, safer refactoring, and clearer audit trails for security-sensitive operations.

  1.4 **SHOULD** use a **command/subcommand** architecture for multi-action tools (e.g., `tool init`, `tool run`).  
  **Rationale**: Scales the user interface without breaking existing scripts or polluting the global option namespace.

### 2. CLI contract and interface design

2.1 **MUST** provide `--help` (and `-h` unless domain conventions conflict) and `--version`, exiting with status `0`.  
**Rationale**: Standard discoverability for users and automation; required by POSIX Utility Conventions.

2.2 **MUST** implement a stable, documented CLI contract including:

- Option names, defaults, and required arguments.
- Exit codes and their meanings.
- Output format stability guarantees (especially for machine-readable modes).  
  **Rationale**: CLI users write scripts around these contracts; changing them constitutes breaking changes.

  2.3 **MUST** follow POSIX/GNU conventions:

- Long options use double hyphens (`--verbose`); short options use single (`-v`).
- Options precede operands where reasonable.
- Support `--` to terminate option parsing (protecting filenames starting with hyphens).
- Support option clustering for short flags if implemented (e.g., `-abc` equivalent to `-a -b -c`).  
  **Rationale**: Minimizes user surprise, ensures compatibility with standard shell completions, and prevents argument injection vulnerabilities.

  2.4 **MUST** define configuration precedence (highest to lowest):  
  `CLI flags` > `Environment variables` > `Configuration files` > `Compiled defaults`.  
  **Rationale**: Predictable override behavior enables temporary adjustments (flags) without modifying persistent state (config files).

  2.5 **SHOULD** provide **machine-readable output** modes (e.g., `--json`, `--tsv`, `--format=yaml`) and ensure they remain stable across versions.  
  **Rationale**: Prevents brittle text scraping in automation and enables reliable tool chaining.

  2.6 **MUST** avoid interactive prompts when `stdin` is not a TTY; provide `--yes`, `--no`, or `--non-interactive` flags for automation.  
  **Rationale**: Prevents CI/CD pipeline hangs and ensures composability in scripts.

### 3. Input/output, streams, and terminal handling

3.1 **MUST** write **primary data output** to **stdout** and **diagnostics/errors** to **stderr**.  
**Rationale**: Enables correct piping (`tool | grep pattern`) and redirection without corrupting data streams or losing error context.

3.2 **MUST** ensure machine-readable output modes write **only** structured data to stdout; all diagnostics, progress bars, and warnings must go to stderr.  
**Rationale**: Prevents parsing errors in downstream tools consuming JSON/CSV output.

3.3 **MUST** end human-readable lines with `\n` (POSIX text line termination).  
**Rationale**: Ensures compatibility with standard Unix text processing tools (`grep`, `awk`, `wc`) and prevents prompt rendering issues.

3.4 **SHOULD** adapt output to terminal width when displaying tables or wrapped text:

- Detect TTY and terminal width.
- Provide `--no-wrap` or `--width` overrides for deterministic logs.  
  **Rationale**: Improves usability in constrained terminals without breaking automation in pipes.

  3.5 **MUST** treat color and styling as optional:

- Default to **no color** when stdout is not a TTY.
- Support `--color=auto|always|never` (or equivalent).
- Respect the `NO_COLOR` environment variable.  
  **Rationale**: Accessibility (low vision, color blindness), log file readability (no escape codes), and piping safety.

  3.6 **MUST** never rely on color alone to convey meaning; include text cues, labels, or icons.  
  **Rationale**: Ensures accessibility for color-blind users and readability in monochrome logs.

### 4. Error handling, exit codes, and diagnostics

4.1 **MUST** exit with status `0` for success and non-zero for failure.  
**Rationale**: Fundamental scripting contract; shell conditionals (`&&`, `||`) depend on this semantics.

4.2 **MUST** document exit codes and keep them stable across versions.  
**Rationale**: Scripts depend on specific codes for error handling; changing codes breaks automation.

4.3 **MUST** distinguish error categories with appropriate codes:

- `0`: Success.
- `1`: General error (catchall).
- `2`: CLI usage error (invalid arguments, unknown options).
- `126`: Command invoked cannot execute (permission denied).
- `127`: Command not found (missing dependency).
- `128+N`: Fatal error signal (e.g., `130` for SIGINT, `143` for SIGTERM).  
  **Rationale**: Aligns with `sysexits.h` conventions and ecosystem expectations, enabling precise error handling in calling scripts.

  4.4 **MUST** handle `SIGINT` (Ctrl+C) gracefully for long-running operations:

- Perform cleanup (temporary files, locks).
- Exit with status `130` (128 + `SIGINT` value 2).  
  **Rationale**: Prevents resource leaks and corrupted partial state; meets user expectations for interruption behavior.

  4.5 **MUST** avoid printing stack traces or debug dumps by default in user-facing modes; provide `--debug`, `--verbose`, or `-v` flags for diagnostics.  
  **Rationale**: Improves user experience and prevents accidental leakage of sensitive implementation details.

  4.6 **MUST** ensure error messages are actionable:

- State what failed and why.
- Include relevant context (file paths, values) where safe.
- Suggest corrective action when possible.  
  **Rationale**: Reduces support burden and speeds recovery without requiring source code inspection.

### 5. Security and safety

5.1 **MUST** treat all external input as untrusted: CLI arguments, environment variables, config files, `stdin`, and file contents.  
**Rationale**: CLIs frequently process attacker-controlled strings in automation contexts; trust boundaries must be explicit.

5.2 **MUST** prevent shell injection:

- Never pass user input through shell interpreters (`system()`, `sh -c`, `eval`) unless unavoidable.
- Prefer `execve`-family calls or safe APIs with argument arrays.
- If shell use is required, strictly quote/escape input using language-specific utilities (e.g., `shlex.quote` in Python).  
  **Rationale**: Prevents remote code execution, data exfiltration, and privilege escalation.

  5.3 **MUST** handle filesystem operations safely:

- Use atomic writes (write to temp file, `fsync`, then `rename`) for critical data.
- Use secure temporary file creation (`mkstemp`, `tempfile` modules) with restrictive permissions (0600).
- Canonicalize paths and validate against directory traversal (`../`, symlinks) when accessing user-provided paths.  
  **Rationale**: Prevents Time-of-Check to Time-of-Use (TOCTOU) race conditions, data corruption, and unauthorized file access.

  5.4 **MUST** accept secrets (passwords, API keys, tokens) only via environment variables, secure configuration files, or interactive `stdin` prompts (using `getpass` or equivalent), **never** via command-line arguments.  
  **Rationale**: Command-line arguments are visible to all users via `ps`, process monitors, and shell history; this prevents credential exposure.

  5.5 **MUST** redact secrets in logs, error messages, and debug output.  
  **Rationale**: CLI output is frequently captured in CI logs and shared; redaction prevents credential leakage.

  5.6 **SHOULD** implement "dry run" modes (`--dry-run`, `-n`) for destructive operations and provide clear descriptions of what would change.  
  **Rationale**: Reduces irreversible mistakes in production environments and automation.

### 6. Performance, streaming, and resource management

6.1 **MUST** stream large inputs/outputs; avoid loading entire files into memory unless size is bounded and documented.  
**Rationale**: CLIs process multi-gigabyte files and operate in memory-constrained environments; streaming ensures scalability.

6.2 **MUST** keep startup time low by avoiding unnecessary imports, network calls, or heavy initialization on common code paths.  
**Rationale**: CLIs are invoked frequently in loops and pipelines; latency compounds quickly.

6.3 **SHOULD** provide progress indicators only when stderr is a TTY, and ensure they do not corrupt piped output (use `\r` for updates, clear on completion).  
**Rationale**: Progress bars improve UX for long operations but create log pollution in non-interactive contexts.

6.4 **MAY** implement concurrency, but **MUST** keep output deterministic or explicitly document nondeterminism and provide ordering options.  
**Rationale**: Prevents flaky scripts and confusing logs when output order matters.

### 7. Portability and environment

7.1 **MUST** follow the **XDG Base Directory Specification**:

- Config: `$XDG_CONFIG_HOME/<app>/` (fallback `~/.config/<app>/`).
- Data: `$XDG_DATA_HOME/<app>/` (fallback `~/.local/share/<app>/`).
- Cache: `$XDG_CACHE_HOME/<app>/` (fallback `~/.cache/<app>/`).  
  **Rationale**: Aligns with modern Unix conventions, keeps home directories tidy, and supports proper backup/ignore patterns.

  7.2 **MUST** avoid non-portable assumptions:

- File path separators (use libraries, not hardcoded `/`).
- Line endings (handle CRLF gracefully where possible).
- Locale/encoding (assume UTF-8 but handle invalid sequences gracefully).
- Filesystem case sensitivity.  
  **Rationale**: POSIX-like systems differ materially (Linux vs. macOS vs. BSD); portability ensures broader utility.

  7.3 **Shell scripts MUST**:

- Use `#!/bin/sh` for POSIX compliance or `#!/usr/bin/env bash` if Bash-specific features are required (documented).
- Avoid Bashisms (`[[ ]]`, arrays, `source`, process substitution) in `sh` scripts.
- Use safe quoting (`"$var"`) and check for undefined variables (`set -u` or equivalent).  
  **Rationale**: `/bin/sh` may be `dash`, `ash`, or `ksh` on various systems; Bashisms cause portability failures.

  7.4 **SHOULD** respect locale environment variables (`LC_ALL`, `LC_CTYPE`, `LANG`) for human-facing output (dates, sorting), but keep machine-readable modes locale-invariant.  
  **Rationale**: Human usability without breaking automation that depends on consistent formatting.

### 8. Configuration and environment variables

8.1 **MUST** namespace environment variables (e.g., `TOOLNAME_API_KEY`) and document them in `--help` or man pages.  
**Rationale**: Prevents collisions with other tools and hidden configuration behavior.

8.2 **MUST** validate configuration files early and fail fast with clear messages pointing to the exact key/file causing the error.  
**Rationale**: Prevents subtle misbehavior and confusing runtime failures due to typos or invalid formats.

8.3 **MUST** avoid surprising implicit config file discovery; if searching multiple locations, list them in `--verbose` or `--debug` output.  
**Rationale**: Predictability and debuggability; users must understand why a specific configuration is being applied.

### 9. Testing strategy

9.1 **MUST** test core logic independently from CLI parsing (unit tests).  
**Rationale**: Fast, deterministic tests that don't require subprocess overhead.

9.2 **MUST** include integration tests that execute the compiled/binary CLI as a subprocess, asserting:

- Correct stdout/stderr separation.
- Specific exit codes for success and failure modes.
- Stable machine-readable output formats.  
  **Rationale**: Verifies the actual user-visible contract, including argument parsing and stream handling.

  9.3 **SHOULD** include "golden" tests for `--help` output and complex formatting to ensure stability.  
  **Rationale**: Prevents accidental changes to user-facing documentation and output formats.

### 10. Documentation and help text

10.1 **MUST** make `--help` output complete and accurate:

- Usage synopsis.
- Description of commands and options.
- Examples for common tasks.
- Notes on stdin/stdout behavior and exit codes.  
  **Rationale**: Users should not need the source code or internet access to use the tool safely.

  10.2 **SHOULD** provide a man page (troff format) or comprehensive `README.md` including:

- Installation instructions.
- Quick start examples.
- Security notes for risky options.
- Configuration and environment variable reference.  
  **Rationale**: Supports adoption, reduces operational risk, and aligns with Unix documentation conventions.

  10.3 **MUST** include inline documentation (docstrings/comments) for public functions explaining purpose, parameters, return values, and exceptions.  
  **Rationale**: Enables automated documentation generation and reduces onboarding friction for maintainers.

### 11. Code quality and automation

11.1 **MUST** use automated formatting and linting tools appropriate to the language; do not rely on manual style enforcement.  
**Rationale**: Consistency across teams and LLM outputs; eliminates bikeshedding and catches syntax errors early.

11.2 **MUST** keep code readable: clear naming, small functions, minimal global state, and no dead code.  
**Rationale**: Maintainability and reviewability reduce long-term technical debt.

11.3 **SHOULD** document non-obvious decisions inline with brief comments linking to specifications (e.g., POSIX, RFCs) when behavior is subtle.  
**Rationale**: Reduces cognitive load for future maintainers and prevents well-intentioned "fixes" that violate standards.

## Appendices

### Appendix A: Application instructions

**When generating new CLI code:**

1. Confirm or infer (and state explicitly):
   - Target language/runtime and minimum version.
   - Whether strict POSIX `sh` or Bash is required.
   - OS targets and portability constraints.
   - Required subcommands and options.
   - Output modes (human vs. machine-readable).

2. Produce:
   - Brief CLI specification (commands, options, env vars, config precedence, exit codes).
   - Implementation code adhering to all **MUST** standards.
   - Usage examples demonstrating piped and scripted use.
   - Testing approach with at least one integration test example.

**When reviewing existing code:**

1. Output sections in this order:
   - **Summary** (1–3 bullets of high-level assessment).
   - **Blocking issues (MUST violations)**: Each includes _what_, _why (rationale)_, and a concrete fix.
   - **Needs justification (SHOULD violations)**: Deviations and what justification would be acceptable.
   - **Optional improvements (MAY)**.
   - **Proposed patch** as a unified diff when feasible.

2. Format violations as:
   ```markdown
   ### [Section X.Y] Standard Name

   - **Violation**: Description
   - **Location**: File:Line or function name
   - **Fix**: Corrected code or diff
   ```

**When constraints conflict:**

- Prefer: POSIX portability > security > stdout/stderr correctness > stable CLI contract > performance.
- If user demands behavior violating a **MUST**, explicitly flag the violation and propose a compliant alternative.

### Appendix B: Enforcement checklist

Critical **MUST** items for quick validation:

- [ ] **Architecture**: Core logic separated from CLI parsing and I/O.
- [ ] **Interface**: `--help` and `--version` implemented and accurate.
- [ ] **Streams**: Normal output to stdout; errors/diagnostics to stderr.
- [ ] **Exit Codes**: `0` for success; non-zero for failure; `130` for SIGINT; documented meanings.
- [ ] **Arguments**: Supports `--` terminator; follows POSIX long/short option conventions.
- [ ] **Interactivity**: Non-interactive-safe defaults (no prompts when stdin is non-TTY).
- [ ] **Accessibility**: Color optional (`--color=auto|always|never`); respects `NO_COLOR`; no color-only meaning.
- [ ] **Security**: Input sanitized (no shell injection); secrets not passed via CLI args; safe file operations (atomic writes, secure temp files).
- [ ] **Portability**: XDG directories used; POSIX `sh` scripts avoid Bashisms; UTF-8 handling graceful.
- [ ] **Performance**: Large data streamed, not fully buffered; startup time minimized.
- [ ] **Configuration**: Environment variables namespaced; precedence documented.
- [ ] **Testing**: Integration tests cover exit codes and stdout/stderr separation.
- [ ] **Documentation**: `--help` complete; inline comments for non-obvious logic.

### Appendix C: Examples

#### C.1 stdout vs stderr separation

**Non-compliant** (breaks pipes):

```python
# Error goes to stdout, corrupting data stream
print("ERROR: File not found")
sys.exit(1)
```

**Compliant**:

```python
import sys

# Data to stdout
print("result,data,value")

# Errors to stderr
print("error: unable to read config", file=sys.stderr)
sys.exit(1)
```

#### C.2 Exit codes and signal handling

**Non-compliant** (returns 0 on error, no SIGINT handling):

```python
if bad_args:
    print("bad args")
    sys.exit(0)  # Wrong: scripts will think this succeeded
```

**Compliant**:

```python
import sys
import signal

def handle_sigint(signum, frame):
    sys.stderr.write("\nInterrupted\n")
    sys.exit(130)  # 128 + SIGINT(2)

signal.signal(signal.SIGINT, handle_sigint)

if bad_args:
    sys.stderr.write("error: invalid arguments (see --help)\n")
    sys.exit(2)  # Usage error per convention
```

#### C.3 Shell safety (POSIX `sh`)

**Non-compliant** (word-splitting, injection risk):

```sh
rm -rf $TARGET  # Unquoted, unvalidated
```

**Compliant**:

```sh
#!/bin/sh
# Validate before destructive action
case "$TARGET" in
  ""|"/"|"."|"/.."|"..")
    printf '%s\n' "error: refusing unsafe TARGET=$TARGET" >&2
    exit 2
    ;;
esac
# Safe quoting prevents word-splitting and globbing
rm -rf -- "$TARGET"
```

#### C.4 Machine-readable mode stability

**Non-compliant** (mixing human text with JSON):

```python
print("Loading data...")  # Goes to stdout, breaks JSON parsing
print(json.dumps(data))
```

**Compliant**:

```python
import sys
import json

# Diagnostics to stderr
print("loading data...", file=sys.stderr)

# Clean machine output to stdout
json.dump(data, sys.stdout)
print()  # Ensure trailing newline
```

#### C.5 Secrets handling

**Non-compliant** (exposure via ps):

```python
parser.add_argument("--password", required=True)
args = parser.parse_args()
# Password visible in: ps aux | grep mytool
```

**Compliant**:

```python
import os
import getpass

# Option 1: Environment variable
api_key = os.environ.get("TOOLNAME_API_KEY")
if not api_key:
    # Option 2: Interactive prompt (not visible)
    api_key = getpass.getpass("API Key: ")
```

#### C.6 Atomic file writes

**Non-compliant** (risk of corruption):

```python
with open("config.json", "w") as f:
    f.write(json.dumps(data))  # If killed here, file is empty/truncated
```

**Compliant**:

```python
import tempfile
import os

def write_atomic(filepath, data):
    dir_name = os.path.dirname(os.path.abspath(filepath)) or "."
    fd, tmp_path = tempfile.mkstemp(dir=dir_name, prefix=".tmp_")
    try:
        with os.fdopen(fd, "w") as f:
            f.write(data)
            f.flush()
            os.fsync(fd)
        os.rename(tmp_path, filepath)
    except Exception:
        os.unlink(tmp_path)
        raise
```
