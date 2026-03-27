# Dignified Python Reference Documentation

Production-quality Python coding standards for writing clean, maintainable, modern Python code.

## Table of Contents

### Core Standards

- **[dignified-python-core.md](../dignified-python-core.md)** - Essential Python standards (always apply)
  - Modern type syntax (list[str], str | None)
  - LBYL exception handling patterns
  - Pathlib operations
  - Absolute imports
  - Error boundaries

- **[cli-patterns.md](../cli-patterns.md)** - Command-line interface patterns
  - Click usage patterns
  - Argparse best practices
  - CLI error handling
  - Configuration management

### Version-Specific Features (`versions/`)

- **[python-3.6.md](../versions/python-3.6.md)** - Python 3.6-3.9 compatibility baseline
  - typing.Union / Optional / List / Dict syntax
  - String forward references (no future annotations)
  - typing-extensions (max 4.1.1)

- **[python-3.10.md](../versions/python-3.10.md)** - Python 3.10+ features
  - Structural pattern matching (match/case)
  - Parenthesized context managers
  - Better error messages
  - Type union operator (X | Y)

- **[python-3.11.md](../versions/python-3.11.md)** - Python 3.11+ features
  - Exception groups (ExceptionGroup)
  - except\* syntax
  - Self type
  - Variadic generics

- **[python-3.12.md](../versions/python-3.12.md)** - Python 3.12+ features
  - Type parameter syntax (Generic[T])
  - Override decorator
  - Per-interpreter GIL
  - f-string improvements

- **[python-3.13.md](../versions/python-3.13.md)** - Python 3.13+ features
  - Experimental free-threading
  - JIT compilation
  - Improved error messages
  - Performance improvements

### Advanced Topics (`advanced/`)

- **[exception-handling.md](./advanced/exception-handling.md)** - Exception patterns
  - LBYL (Look Before You Leap) patterns
  - Error boundaries
  - Exception chaining
  - Custom exceptions

- **[interfaces.md](./advanced/interfaces.md)** - Interface design
  - ABC (Abstract Base Class) patterns
  - Protocol types
  - Gateway layer interfaces
  - Type narrowing

- **[typing-advanced.md](./advanced/typing-advanced.md)** - Advanced typing
  - Generic types
  - Type narrowing
  - Literal types
  - TypedDict and dataclasses

- **[api-design.md](./advanced/api-design.md)** - API design principles
  - Function signatures
  - Parameter complexity
  - Code organization
  - Production-tested patterns

## Philosophy

### Core Principles

**Modern Type Syntax**: Use Python 3.10+ type syntax when the target runtime is 3.10+. For
3.6-3.9 compatibility, follow `versions/python-3.6.md`.

```python
# Good (modern)
def process(items: list[str]) -> str | None:
    pass

# Avoid (legacy)
from typing import List, Optional
def process(items: List[str]) -> Optional[str]:
    pass
```

**LBYL Over EAFP**: Look Before You Leap, not Easier to Ask for Forgiveness than Permission

```python
# Good (LBYL)
if path.exists():
    content = path.read_text()

# Avoid (EAFP)
try:
    content = path.read_text()
except FileNotFoundError:
    pass
```

**Pathlib Over os.path**: Use pathlib for all file operations

```python
# Good
from pathlib import Path
config_path = Path("config.yaml")
if config_path.exists():
    content = config_path.read_text()

# Avoid
import os
if os.path.exists("config.yaml"):
    with open("config.yaml") as f:
        content = f.read()
```

**Absolute Imports**: Never use relative imports

```python
# Good
from myproject.utils import helper

# Avoid
from .utils import helper
from ..shared import helper
```

**Error Boundaries at CLI Level**: Handle errors at the CLI entry point, not deep in the stack

## Quick Reference

### Type Annotations

**Python 3.10+ (modern syntax)**

```python
# Basic types
def greet(name: str) -> str:
    return f"Hello, {name}"

# Collections (modern syntax)
def process(items: list[str], mapping: dict[str, int]) -> tuple[str, int]:
    pass

# Optional/Union (modern syntax)
def find(query: str) -> str | None:
    pass

# Multiple types
def parse(value: str | int | float) -> float:
    pass
```

**Python 3.6-3.9 compatible**

```python
from typing import Dict, List, Optional, Tuple, Union

def greet(name: str) -> str:
    return "Hello, %s" % (name,)

def process(items: List[str], mapping: Dict[str, int]) -> Tuple[str, int]:
    pass

def find(query: str) -> Optional[str]:
    pass

def parse(value: Union[str, int, float]) -> float:
    pass
```

### LBYL Patterns

```python
# File operations
if path.exists():
    content = path.read_text()

# Dictionary access
if "key" in data:
    value = data["key"]

# Attribute access
if hasattr(obj, "method"):
    obj.method()

# Type checking
if isinstance(value, str):
    result = value.upper()
```

### Pathlib Operations

```python
from pathlib import Path

# Create path
config = Path("config.yaml")
data_dir = Path("/data")

# Check existence
if config.exists():
    pass

# Read/write
content = config.read_text()
config.write_text("data")

# Directory operations
for file in data_dir.glob("*.txt"):
    print(file.name)

# Path manipulation
full_path = data_dir / "subdir" / "file.txt"
parent = full_path.parent
name = full_path.name
```

### CLI Patterns (Click)

```python
import click

@click.command()
@click.option("--name", required=True, help="User name")
@click.option("--count", default=1, help="Number of times")
def greet(name: str, count: int) -> None:
    """Greet a user multiple times."""
    for _ in range(count):
        click.echo(f"Hello, {name}!")

if __name__ == "__main__":
    greet()
```

## Version Detection

**Version detection** determines which Python version features are available:

1. Prefer the closest `pyproject.toml` (walk up from the file you're editing) and read
   `requires-python`
2. Otherwise, use the closest `setup.py` / `setup.cfg` and read `python_requires`
3. Otherwise, check `.python-version`
4. Default to Python 3.12 if not specified

Based on detected version, appropriate version-specific features are recommended.

**Multi-version repositories:** Some repos run tooling on 3.10+ but keep a core library compatible
with 3.6. In that case, the *minimum supported runtime version for the code you're editing*
controls the allowed syntax. Use `versions/python-3.6.md` for 3.6-3.9 compatibility zones.

## Navigation Tips

- **Start with dignified-python-core.md** for essential patterns that apply to all code
- **Check version-specific docs** based on your project's Python version
- **Reference advanced topics** when dealing with specialized patterns
- **Use cli-patterns.md** when building command-line tools

## When to Read Each Reference

| Situation                  | Reference                      |
| -------------------------- | ------------------------------ |
| Writing any Python code    | dignified-python-core.md        |
| Building a CLI tool        | cli-patterns.md                |
| Supporting Python 3.6-3.9  | versions/python-3.6.md         |
| Using Python 3.10 features | versions/python-3.10.md        |
| Using Python 3.11 features | versions/python-3.11.md        |
| Using Python 3.12 features | versions/python-3.12.md        |
| Using Python 3.13 features | versions/python-3.13.md        |
| Handling exceptions        | advanced/exception-handling.md |
| Designing interfaces       | advanced/interfaces.md         |
| Complex type hints         | advanced/typing-advanced.md    |
| API design decisions       | advanced/api-design.md         |

## Production Patterns

These standards reflect production-tested patterns from real-world Python codebases:

- ✅ **Modern type syntax** - Improves IDE support and type checking
- ✅ **LBYL patterns** - More explicit and easier to debug than EAFP
- ✅ **Pathlib** - More readable and cross-platform than os.path
- ✅ **Absolute imports** - Avoid import confusion and relative import issues
- ✅ **Error boundaries at CLI** - Clean error messages for end users

## Documentation Structure

Each reference document follows a consistent structure:

1. **Overview** - High-level concepts
2. **Patterns** - Common code patterns with examples
3. **Best Practices** - Recommended approaches
4. **Anti-Patterns** - What to avoid
5. **Real-World Examples** - Production code samples
6. **Related Topics** - Cross-references

## Scope

This reference is intentionally framework-agnostic: it focuses on clean, maintainable, typed Python
patterns that apply to most production codebases. For framework-specific conventions, consult the
appropriate framework documentation or a dedicated skill for that ecosystem.
