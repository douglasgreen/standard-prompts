# Python project setup guide

Complete setup instructions for Python 3.12+ projects following engineering best practices and documentation standards.

**Document version:** 1.0.0
**Document date:** 2026-02-20
**Reading time:** 25 minutes

---

## Table of contents

- [Overview](#overview)
- [Universal project foundation](#universal-project-foundation)
- [Project type templates](#project-type-templates)
  - [Library package](#library-package)
  - [CLI application](#cli-application)
  - [Web application](#web-application)
  - [Microservice](#microservice)
- [Development workflow](#development-workflow)
- [Quality assurance](#quality-assurance)
- [Documentation structure](#documentation-structure)
- [Appendices](#appendices)

---

## Overview

This guide provides complete, copy-pasteable project setup templates for Python 3.12+ projects. Each template includes directory structure, configuration files, and automation setup.

**Prerequisites:**

- Python 3.12 or later installed
- Git configured
- `uv` installed (`pip install uv`)

**Key principles:**

- Use `src/` layout for all distributable code
- Centralize configuration in `pyproject.toml`
- Automate quality checks with pre-commit hooks
- Validate all inputs at boundaries with Pydantic
- Log to stdout/stderr (no log files)
- Store configuration in environment variables (12-Factor App)

---

## Universal project foundation

Every Python project—regardless of type—requires this common foundation.

### 1. Initialize repository

Create the project directory and initialize version control:

```bash
mkdir my-project
cd my-project
git init
```

### 2. Create directory structure

Run these commands to create the standard layout:

```bash
# Source code (Standard 2.1: src layout)
mkdir -p src/my_project

# Tests (Standard 10.1: Unit/integration separation)
mkdir -p tests/unit tests/integration

# Documentation (Standard 11.2: Diátaxis structure)
mkdir -p docs/tutorials docs/how-to docs/reference docs/explanation

# Infrastructure
mkdir -p .github/workflows

# Essential files
touch src/my_project/__init__.py
touch README.md CHANGELOG.md LICENSE .env.example .gitignore
```

**Resulting structure:**

```text
my-project/
├── pyproject.toml          # Single source of truth (SSOT)
├── uv.lock                 # Reproducible dependencies
├── README.md               # Project overview
├── CHANGELOG.md            # Version history
├── LICENSE                 # License file
├── .env.example            # Environment template (no secrets)
├── .gitignore              # Version control exclusions
├── .pre-commit-config.yaml # Quality automation
├── Makefile                # Common commands
├── src/
│   └── my_project/
│       ├── __init__.py
│       ├── __about__.py    # Version info
│       ├── config.py       # Settings (Standard 1.3)
│       └── logging.py      # Stdout logging
├── tests/
│   ├── conftest.py         # Shared fixtures
│   ├── unit/
│   └── integration/
└── docs/
    ├── index.md
    ├── tutorials/
    ├── how-to/
    ├── reference/
    └── explanation/
```

### 3. Configure pyproject.toml

Create `pyproject.toml` as the single source of truth:

```toml
[build-system]
requires = ["hatchling>=1.24.0"]
build-backend = "hatchling.build"

[project]
name = "my-project"
version = "0.1.0"
description = "Project description."
readme = "README.md"
requires-python = ">=3.12"
license = { file = "LICENSE" }
authors = [{ name = "Your Team", email = "team@example.com" }]

dependencies = [
    "pydantic>=2.6",
    "pydantic-settings>=2.2",
]

[project.optional-dependencies]
dev = [
    "mypy>=1.10",
    "pre-commit>=3.7",
    "pytest>=8.0",
    "pytest-cov>=5.0",
    "ruff>=0.6",
    "pip-audit>=2.7",
]

# --- Tool Configuration ---

[tool.ruff]
line-length = 88
target-version = "py312"

[tool.ruff.lint]
select = [
    "E", "F", "W",   # pycodestyle/pyflakes
    "I",             # import sorting
    "B",             # bugbear
    "UP",            # pyupgrade
    "SIM",           # simplify
    "C4",            # comprehensions
    "S",             # bandit security
]
ignore = []

[tool.ruff.lint.per-file-ignores]
"tests/**.py" = ["S101"]  # Allow assert in tests

[tool.mypy]
python_version = "3.12"
strict = true
warn_unused_configs = true
pretty = true
show_error_codes = true

[tool.pytest.ini_options]
testpaths = ["tests"]
addopts = "-q --cov=my_project --cov-report=term-missing --cov-fail-under=80"

[tool.coverage.run]
branch = true
source = ["src"]
```

### 4. Add configuration module

Create `src/my_project/config.py` for 12-Factor configuration:

```python
"""Application settings loaded from environment variables."""

from pydantic import Field
from pydantic_settings import BaseSettings, SettingsConfigDict


class Settings(BaseSettings):
    """Application configuration.
    
    Attributes:
        environment: Deployment environment (local, staging, prod).
        log_level: Python logging level name.
    """
    
    model_config = SettingsConfigDict(
        env_prefix="MY_PROJECT_",
        extra="forbid",  # Prevent undefined env vars
    )
    
    environment: str = Field(default="local")
    log_level: str = Field(default="INFO")
```

### 5. Add logging configuration

Create `src/my_project/logging.py`:

```python
"""Structured logging to stdout."""

import logging
import sys


def configure_logging(*, level: str) -> None:
    """Configure application logging.
    
    Args:
        level: Logging level name (DEBUG, INFO, WARNING, ERROR).
        
    Raises:
        ValueError: If level is invalid.
    """
    numeric_level = logging.getLevelNamesMapping().get(level.upper())
    if numeric_level is None:
        raise ValueError(f"Invalid log level: {level}")
    
    root = logging.getLogger()
    root.handlers.clear()
    root.setLevel(numeric_level)
    
    handler = logging.StreamHandler(sys.stdout)
    handler.setLevel(numeric_level)
    formatter = logging.Formatter(
        fmt="%(asctime)s %(levelname)s %(name)s %(message)s",
    )
    handler.setFormatter(formatter)
    root.addHandler(handler)
```

### 6. Add environment template

Create `.env.example` (commit this, never commit `.env`):

```bash
# Copy to .env and customize (do not commit .env)
MY_PROJECT_ENVIRONMENT=local
MY_PROJECT_LOG_LEVEL=INFO
```

### 7. Configure pre-commit hooks

Create `.pre-commit-config.yaml`:

```yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v5.0.0
    hooks:
      - id: end-of-file-fixer
      - id: trailing-whitespace
      - id: check-yaml
      - id: check-added-large-files

  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.9.0
    hooks:
      - id: ruff
        args: ["--fix"]
      - id: ruff-format

  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.11.0
    hooks:
      - id: mypy
        additional_dependencies:
          - pydantic>=2.6
          - pydantic-settings>=2.2
```

### 8. Create automation Makefile

Create `Makefile` for common commands:

```makefile
.PHONY: install format lint test check clean

install:
	uv sync --all-extras

format:
	uv run ruff format .
	uv run ruff check --fix .

lint:
	uv run ruff check .
	uv run mypy src

test:
	uv run pytest

check: format lint test

clean:
	rm -rf .venv .pytest_cache .ruff_cache .coverage htmlcov dist
```

### 9. Initialize project

Run these commands to complete setup:

```bash
# Create virtual environment and install dependencies
uv venv
uv pip install -e ".[dev]"

# Generate lockfile
uv lock

# Install git hooks
pre-commit install

# Verify setup
make check
```

---

## Project type templates

Choose the template that matches your project type. Each extends the universal foundation.

### Library package

Use this for reusable packages published to PyPI or private repositories.

**Key differences from foundation:**

- Minimal dependencies (avoid pinning where possible)
- Public API exposed in `__init__.py`
- Type marker file (`py.typed`) for PEP 561 compliance
- Focus on backward compatibility

**Update `pyproject.toml`:**

Add to `[project]`:

```toml
keywords = ["your-domain"]
classifiers = [
    "Programming Language :: Python :: 3 :: Only",
    "Programming Language :: Python :: 3.12",
    "Typing :: Typed",
]

[tool.hatch.build.targets.wheel]
packages = ["src/my_project"]
```

**Create `src/my_project/__about__.py`:**

```python
"""Version information."""

__version__ = "0.1.0"
```

**Create `src/my_project/__init__.py`:**

```python
"""My Project - Short description.

This package provides...
"""

from my_project.__about__ import __version__
from my_project.core import main_function

__all__ = ["__version__", "main_function"]
```

**Create `src/my_project/py.typed`:**

```text
# Marker file for PEP 561 type checking support
```

**Add test in `tests/unit/test_core.py`:**

```python
"""Tests for core functionality."""

from my_project import __version__


def test_version() -> None:
    """Verify version is string."""
    assert isinstance(__version__, str)
```

---

### CLI application

Use this for command-line tools.

**Add dependency:**

```bash
uv add "typer[all]"
```

**Update `pyproject.toml`:**

Add console script entry point:

```toml
[project.scripts]
my-cli = "my_project.cli:app"
```

**Create `src/my_project/cli.py`:**

```python
"""Command-line interface."""

from typing import Annotated

import typer

from my_project.config import Settings
from my_project.logging import configure_logging

app = typer.Typer(no_args_is_help=True)


@app.command()
def main(
    name: Annotated[str, typer.Argument(help="Name to greet")],
    no_color: Annotated[
        bool, 
        typer.Option("--no-color", help="Disable color output")
    ] = False,
) -> None:
    """Greet the user.
    
    Args:
        name: Name to include in greeting.
        no_color: Disable colored output for accessibility.
    """
    settings = Settings()
    configure_logging(level=settings.log_level)
    
    # Output must work without color (Standard 12.1)
    message = f"Hello, {name}!"
    if no_color:
        print(message)
    else:
        typer.echo(typer.style(message, fg=typer.colors.GREEN))


if __name__ == "__main__":
    app()
```

**Test the CLI:**

```bash
# Run via module
uv run python -m my_project.cli World

# Run via installed script (after install)
my-cli World --no-color
```

---

### Web application

Use this for FastAPI-based web applications.

**Add dependencies:**

```bash
uv add fastapi uvicorn[standard]
uv add --dev httpx  # For testing
```

**Create directory structure:**

```bash
mkdir -p src/my_project/interfaces src/my_project/domain
```

**Create `src/my_project/interfaces/api.py`:**

```python
"""HTTP interface layer."""

from fastapi import FastAPI

from my_project.config import Settings

settings = Settings()

app = FastAPI(
    title="My Project",
    version="0.1.0",
    docs_url="/docs" if settings.environment == "local" else None,
)


@app.get("/health")
async def health_check() -> dict[str, str]:
    """Health check endpoint.
    
    Returns:
        Status object for load balancers.
    """
    return {"status": "ok"}
```

**Create `src/my_project/__main__.py`:**

```python
"""Entry point for web server."""

import uvicorn

from my_project.config import Settings

settings = Settings()

if __name__ == "__main__":
    uvicorn.run(
        "my_project.interfaces.api:app",
        host="0.0.0.0",
        port=8000,
        reload=settings.environment == "local",
    )
```

**Run the server:**

```bash
uv run python -m my_project
```

**Access API documentation:**

Open http://localhost:8000/docs when running locally.

---

### Microservice

Use this for backend services emphasizing 12-Factor principles and async I/O.

**Key additions:**

- Database integration (SQLAlchemy 2.0+ with async)
- Message queue support
- Structured JSON logging
- Health checks for dependencies

**Add dependencies:**

```bash
uv add sqlalchemy[asyncio] asyncpg alembic
uv add structlog  # Structured logging
```

**Create `src/my_project/infrastructure/database.py`:**

```python
"""Database connection management."""

from collections.abc import AsyncGenerator

from sqlalchemy.ext.asyncio import (
    AsyncEngine,
    AsyncSession,
    async_sessionmaker,
    create_async_engine,
)
from sqlalchemy.orm import DeclarativeBase

from my_project.config import Settings

settings = Settings()


class Base(DeclarativeBase):
    """Base for all models."""


def create_engine() -> AsyncEngine:
    """Create async database engine."""
    return create_async_engine(
        str(settings.database_url),
        echo=settings.environment == "local",
        pool_pre_ping=True,
    )


async def get_session() -> AsyncGenerator[AsyncSession, None]:
    """Provide database session for dependency injection."""
    engine = create_engine()
    session_factory = async_sessionmaker(engine, expire_on_commit=False)
    
    async with session_factory() as session:
        try:
            yield session
            await session.commit()
        except Exception:
            await session.rollback()
            raise
        finally:
            await session.close()
```

**Update `src/my_project/config.py`:**

Add database URL:

```python
from pydantic import PostgresDsn

# Add to Settings class:
database_url: PostgresDsn
```

---

## Development workflow

### Daily development cycle

1. **Create feature branch:**

   ```bash
   git checkout -b feature/description
   ```

2. **Make changes** following type hints strictly.

3. **Run quality checks:**

   ```bash
   make check
   ```

4. **Commit** (pre-commit hooks run automatically):

   ```bash
   git add .
   git commit -m "feat: description"
   ```

### Dependency management

**Add runtime dependency:**

```bash
uv add package-name
```

**Add development dependency:**

```bash
uv add --dev package-name
```

**Update lockfile after any change:**

```bash
uv lock
```

---

## Quality assurance

### Required local checks

Run these before committing:

| Command | Purpose |
|:--------|:--------|
| `uv run ruff format .` | Format code |
| `uv run ruff check .` | Lint code |
| `uv run mypy src` | Type check |
| `uv run pytest` | Run tests |
| `uv run pip-audit` | Security audit |

### CI/CD pipeline

Create `.github/workflows/ci.yml`:

```yaml
name: CI

on: [push, pull_request]

jobs:
  quality:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Install uv
        uses: astral-sh/setup-uv@v1
      
      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.12"
      
      - name: Install dependencies
        run: uv sync --all-extras --dev
      
      - name: Check formatting
        run: uv run ruff format --check .
      
      - name: Lint
        run: uv run ruff check .
      
      - name: Type check
        run: uv run mypy src
      
      - name: Test
        run: uv run pytest
      
      - name: Security audit
        run: uv run pip-audit
```

---

## Documentation structure

Follow the Diátaxis framework in your `docs/` directory:

### Required files

**`docs/index.md`** (navigation hub):

```markdown
---
title: Documentation
description: Project documentation hub
last_reviewed: 2026-02-08
---

# Documentation

## Tutorials
- [Set up your environment](./tutorials/setup.md)

## How-to guides
- [Run tests](./how-to/testing.md)

## Reference
- [API documentation](./reference/api.md)

## Explanation
- [Architecture decisions](./explanation/adr-001.md)
```

**`docs/tutorials/setup.md`** (learning-oriented):

```markdown
---
title: Set up your environment
description: Complete environment setup tutorial
---

# Set up your environment

## Prerequisites

- Python 3.12+
- uv installed

## Steps

1. Clone the repository
2. Run `make install`
3. Run `make check` to verify

## Verification

You should see all checks pass.
```

**`docs/how-to/testing.md`** (task-oriented):

```markdown
---
title: Run tests
description: How to run the test suite
---

# Run tests

## Unit tests only

```bash
uv run pytest -m "not integration"
```

## All tests with coverage

```bash
uv run pytest --cov
```
```

---

## Appendices

### Appendix A: Quick reference

| Task | Command |
|:-----|:--------|
| Install dependencies | `make install` or `uv sync` |
| Run all checks | `make check` |
| Format code | `uv run ruff format .` |
| Type check | `uv run mypy src` |
| Run tests | `uv run pytest` |
| Build package | `uv build` |

### Appendix B: Compliance checklist

Before committing, verify:

- [ ] `src/` layout present with `__init__.py`
- [ ] `pyproject.toml` contains all tool configurations
- [ ] `uv.lock` committed (reproducible builds)
- [ ] `.env.example` present with no real secrets
- [ ] `mypy --strict` passes
- [ ] `ruff format` and `ruff check` pass
- [ ] `pytest` passes with 80%+ coverage
- [ ] Documentation follows Diátaxis structure
- [ ] All code blocks have language tags
- [ ] No credentials in code or examples

### Appendix C: Troubleshooting

**Import errors in tests:**

```bash
# Ensure package is installed in editable mode
uv pip install -e ".[dev]"
```

**mypy missing imports:**

```bash
# Add to pyproject.toml
[[tool.mypy.overrides]]
module = "package_name.*"
ignore_missing_imports = true
```

**Pre-commit hook failures:**

```bash
# Update hooks
pre-commit autoupdate

# Run manually
pre-commit run --all-files
```

---

**Next steps:**

1. Choose your project type template
2. Copy the universal foundation commands
3. Apply the specific template modifications
4. Run `make check` to verify setup
5. Commit the initial structure
