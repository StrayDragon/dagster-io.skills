---
description: Import-time side effects, @cache for deferred computation, module-level code patterns.
---

# Module Design Reference

**Read when**: Creating new modules, adding module-level code, using a caching decorator (@cache / @lru_cache)

---

**Version Note**: Examples in this document default to Python 3.10+ type syntax. If you are editing
Python 3.6-3.9 compatible code, apply the same rules but translate type syntax per
`versions/python-3.6.md` (e.g., `X | None` → `Optional[X]`).

Some code blocks below are illustrative fragments and may omit app-specific stubs. For a
copy-paste runnable example, see **Copy-Paste Runnable Demo (Python 3.6+)**.

---

## Import-Time Side Effects

### Core Rule

**Avoid computation and side effects at import time. Defer to function calls.**

Module-level code runs when the module is imported. Side effects at import time cause:

1. **Slower startup** - Every import triggers computation
2. **Test brittleness** - Hard to mock/control behavior
3. **Circular import issues** - Dependencies evaluated too early
4. **Unpredictable order** - Import order affects behavior

---

## Common Anti-Patterns

```python
from pathlib import Path
from typing import Optional

# WRONG: Path computed at import time
SESSION_ID_FILE = Path(".cache/myapp/current-session-id")

def get_session_id() -> Optional[str]:
    if SESSION_ID_FILE.exists():
        return SESSION_ID_FILE.read_text(encoding="utf-8")
    return None

# WRONG: Config loaded at import time
CONFIG = load_config()  # I/O at import!

# WRONG: Connection established at import time
DB_CLIENT = DatabaseClient(os.environ["DB_URL"])  # Side effect at import!
```

---

## Correct Patterns

**Use a caching decorator for deferred computation:**

**Python 3.10+** (`functools.cache`)

```python
from functools import cache
from pathlib import Path

# CORRECT: Defer computation until first call
@cache
def _session_id_file_path() -> Path:
    """Return path to session ID file (cached after first call)."""
    return Path(".cache/myapp/current-session-id")

def get_session_id() -> str | None:
    session_file = _session_id_file_path()
    if session_file.exists():
        return session_file.read_text(encoding="utf-8")
    return None
```

**Python 3.6-3.9 compatible** (`functools.lru_cache`)

```python
from functools import lru_cache
from pathlib import Path
from typing import Optional

@lru_cache(maxsize=None)
def _session_id_file_path() -> Path:
    return Path(".cache/myapp/current-session-id")

def get_session_id() -> Optional[str]:
    session_file = _session_id_file_path()
    if session_file.exists():
        return session_file.read_text(encoding="utf-8")
    return None
```

**Use functions for resources:**

**Python 3.10+**

```python
from functools import cache

# CORRECT: Defer resource creation to function call
@cache
def get_config() -> Config:
    """Load config on first call, cache result."""
    return load_config()

@cache
def get_db_client() -> DatabaseClient:
    """Create database client on first call."""
    return DatabaseClient(os.environ["DB_URL"])
```

**Python 3.6-3.9 compatible**

```python
from functools import lru_cache

@lru_cache(maxsize=None)
def get_config() -> "Config":
    return load_config()

@lru_cache(maxsize=None)
def get_db_client() -> "DatabaseClient":
    return DatabaseClient(os.environ["DB_URL"])
```

### Copy-Paste Runnable Demo (Python 3.6+)

This example avoids import-time I/O and defers work until first call. It runs on Python 3.6+ as-is
(and you can modernize syntax per `versions/python-3.10.md` when your runtime allows it).

```python
import os
from functools import lru_cache
from pathlib import Path
from typing import Any, Dict, Optional

Config = Dict[str, Any]

def load_config() -> Config:
    return {"env": os.environ.get("ENV", "dev")}

class DatabaseClient:
    def __init__(self, url: str) -> None:
        self.url = url

@lru_cache(maxsize=None)
def _session_id_file_path() -> Path:
    return Path(".cache/myapp/current-session-id")

def get_session_id() -> Optional[str]:
    session_file = _session_id_file_path()
    if session_file.exists():
        return session_file.read_text(encoding="utf-8")
    return None

@lru_cache(maxsize=None)
def get_config() -> Config:
    return load_config()

@lru_cache(maxsize=None)
def get_db_client() -> DatabaseClient:
    return DatabaseClient(os.environ.get("DB_URL", "postgres://localhost"))

if __name__ == "__main__":
    print(get_session_id())
    print(get_config()["env"])
    print(get_db_client().url)
```
---

## When Module-Level Constants ARE Acceptable

Simple, static values that don't involve computation or I/O:

```python
# ACCEPTABLE: Static constants
DEFAULT_TIMEOUT = 30
MAX_RETRIES = 3
SUPPORTED_FORMATS = frozenset({"json", "yaml", "toml"})
```

---

## Inline Import Patterns

### Core Rules

1. **Default: ALWAYS place imports at module level**
2. **Use absolute imports only** (no relative imports)
3. **Inline imports only for specific exceptions** (see below)

### Legitimate Inline Import Patterns

#### 1. Circular Import Prevention

```python
# commands/sync.py
def register_commands(cli_group):
    """Register commands with CLI group (avoids circular import)."""
    from myapp.cli import sync_command  # Breaks circular dependency
    cli_group.add_command(sync_command)
```

**When to use:**

- CLI command registration
- Plugin systems with bidirectional dependencies
- Lazy loading to break import cycles

#### 2. Conditional Feature Imports

```python
def process_data(data: dict, dry_run: bool = False) -> None:
    if dry_run:
        # Inline import: Only needed for dry-run mode
        from myapp.dry_run import NoopProcessor
        processor = NoopProcessor()
    else:
        processor = RealProcessor()
    processor.execute(data)
```

**When to use:**

- Debug/verbose mode utilities
- Dry-run mode wrappers
- Optional feature modules
- Platform-specific implementations

#### 3. TYPE_CHECKING Imports

```python
from typing import TYPE_CHECKING

if TYPE_CHECKING:
    from myapp.models import User  # Only for type hints

def process_user(user: "User") -> None:
    ...
```

**When to use:**

- Avoiding circular dependencies in type hints
- Forward declarations

#### 4. Startup Time Optimization (Rare)

Some packages have genuinely heavy import costs (pyspark, jupyter ecosystem, large ML frameworks).
Deferring these imports can improve CLI startup time.

**However, apply "innocent until proven guilty":**

- Default to module-level imports
- Only defer imports when you have MEASURED evidence of startup impact
- Document the measured cost in a comment

```python
# ACCEPTABLE: Measured heavy import (adds 800ms to startup)
def run_spark_job(config: SparkConfig) -> None:
    from pyspark.sql import SparkSession  # Heavy: 800ms import time
    session = SparkSession.builder.getOrCreate()
    ...

# WRONG: Speculative deferral without measurement
def check_staleness(project_dir: Path) -> None:
    # Inline imports to avoid import-time side effects  <- WRONG: no evidence
    from myapp.staleness import get_version
    ...
```

**When NOT to defer:**

- Standard library modules
- Lightweight internal modules
- Modules you haven't measured
- "Just in case" optimization

---

## Decision Checklist

Before writing module-level code:

- [ ] Does this involve any computation (even `Path()` construction)?
- [ ] Does this involve I/O (file, network, environment)?
- [ ] Could this fail or raise exceptions?
- [ ] Would tests need to mock this value?

If any answer is "yes", wrap in a cached function instead (`@cache` on 3.10+, or
`@lru_cache(maxsize=None)` for 3.6-3.9 compatibility).

Before inline imports:

- [ ] Is this to break a circular dependency?
- [ ] Is this for TYPE_CHECKING?
- [ ] Is this for conditional features?
- [ ] If for startup time: Have I MEASURED the import cost?
- [ ] If for startup time: Is the cost significant (>100ms)?
- [ ] If for startup time: Have I documented the measured cost in a comment?
- [ ] Have I documented why the inline import is needed?

**Default: Module-level imports**
