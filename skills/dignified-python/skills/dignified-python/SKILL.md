---
name: dignified-python
description:
  Production Python coding standards with automatic version detection (3.6 and 3.10-3.13). Use when writing,
  reviewing, or refactoring Python to ensure adherence to modern type syntax, LBYL exception
  handling, pathlib operations, ABC-based interfaces, and production-tested patterns. Not
  project-specific - applies to any Python project.
references:
  - dignified-python-core
  - cli-patterns
  - versions/python-3.6
  - versions/python-3.10
  - versions/python-3.11
  - versions/python-3.12
  - versions/python-3.13
  - advanced/api-design
  - advanced/exception-handling
  - advanced/interfaces
  - advanced/typing-advanced
---

# Dignified Python Coding Standards Skill

Production-quality Python coding standards for writing clean, maintainable, modern Python code
(versions 3.6 and 3.10-3.13).

## When to Use This Skill

Auto-invoke when users ask about:

- "make this pythonic" / "is this good python"
- "type hints" / "type annotations" / "typing"
- "LBYL vs EAFP" / "exception handling"
- "pathlib vs os.path" / "path operations"
- "CLI patterns" / "click usage"
- "code review" / "improve this code"
- Any Python code quality or standards question

**Note**: This skill is **general Python standards**, not framework-specific. For framework-level
patterns (web frameworks, orchestration frameworks, etc.), use the appropriate framework-specific
skill or documentation.

## When to Use This Skill vs. Others

| User Need                    | Use This Skill              | Alternative Skill         |
| ---------------------------- | --------------------------- | ------------------------- |
| "make this pythonic"         | ✅ Yes - Python standards   |                           |
| "is this good python"        | ✅ Yes - code quality       |                           |
| "type hints"                 | ✅ Yes - typing guidance    |                           |
| "LBYL vs EAFP"               | ✅ Yes - exception patterns |                           |
| "pathlib vs os.path"         | ✅ Yes - path handling      |                           |
| "best practices for X"       | ❌ No                       | framework-specific skill  |
| "implement feature X"        | ❌ No                       | implementation skill      |
| "which integration to use"   | ❌ No                       | domain expert skill       |
| "CLI argument parsing"       | ✅ Yes - CLI patterns       |                           |

## Core Knowledge (ALWAYS Loaded)

@dignified-python-core.md

## Version Detection

**Identify the minimum supported runtime Python version for the code you're editing** by checking
(in order):

1. `pyproject.toml` - Prefer the closest one (walk up from the file's directory). Look for
   `requires-python` (e.g., `requires-python = ">=3.12"`)
2. `setup.py` or `setup.cfg` - Prefer the closest one. Look for `python_requires`
3. `.python-version` file - Contains version like `3.12` or `3.12.0`
4. Default to Python 3.12 if no version specifier found

**Once identified, load the appropriate version-specific file:**

- Python 3.6-3.9: Load `versions/python-3.6.md`
- Python 3.10: Load `versions/python-3.10.md`
- Python 3.11: Load `versions/python-3.11.md`
- Python 3.12: Load `versions/python-3.12.md`
- Python 3.13: Load `versions/python-3.13.md`

## Multi-Version Repositories (Dev vs Runtime Compatibility)

Some repositories use a newer Python for development (e.g., running tooling on Python 3.10) while a
core library/framework must remain compatible with an older runtime (e.g., Python 3.6).

**Rules:**

1. **The minimum supported runtime version for the code you are editing controls the syntax.**
2. **Detect the target version per package / per directory**, not only repo-wide:
   - Prefer the closest `pyproject.toml` (walking up from the file's directory) and read
     `requires-python`.
   - If absent, use `setup.cfg` / `setup.py` (`python_requires`) in the same nearest-scope way.
   - If the user explicitly states a compatibility floor ("this package must support 3.6"), treat
     that as authoritative.
3. **Shared modules must target the lowest consumer.** If a module is imported by 3.6-compatible
   code, it must be 3.6-compatible even if other parts of the repo use 3.10+.

**Practical mapping:**

- 3.6-3.9 compatible code → follow `versions/python-3.6.md`
- 3.10+ only code (tools, scripts, newer packages) → follow `versions/python-3.10.md` (or newer)

**Note on applying other docs:** Many examples in core/advanced references use 3.10+ type syntax.
When editing 3.6-3.9 compatible code, apply the same *design rules* but translate the type syntax
per `versions/python-3.6.md` (e.g., `list[T]` → `List[T]`, `X | None` → `Optional[X]`) and use
`typing_extensions==4.1.1` for backports when needed.

## Conditional Loading (Load Based on Task Patterns)

Core files above cover 80%+ of Python code patterns. Only load these additional files when you
detect specific patterns:

Pattern detection examples:

- If task mentions "click" or "CLI" -> Load `cli-patterns.md`
- If task mentions "subprocess" -> Load `subprocess.md`

## Reference Documentation Structure

This skill is organized as:

### Core References

- **`dignified-python-core.md`** - Essential standards (always loaded)
- **`cli-patterns.md`** - Command-line interface patterns (click, argparse)
- **`subprocess.md`** - Subprocess handling patterns

### Version-Specific References (`versions/`)

- **`python-3.6.md`** - Type annotation guidance for Python 3.6-3.9 compatibility
- **`python-3.10.md`** - Features available in Python 3.10+
- **`python-3.11.md`** - Features available in Python 3.11+
- **`python-3.12.md`** - Features available in Python 3.12+
- **`python-3.13.md`** - Features available in Python 3.13+

### Advanced Topics (`references/advanced/`)

- **`exception-handling.md`** - LBYL patterns, error boundaries
- **`interfaces.md`** - ABC and Protocol patterns
- **`typing-advanced.md`** - Advanced typing patterns
- **`api-design.md`** - API design principles

## When to Read Each Reference Document

### `references/advanced/exception-handling.md`

**Read when**:

- Writing try/except blocks
- Wrapping third-party APIs that may raise
- Seeing or writing `from e` or `from None`
- Unsure if LBYL alternative exists

### `references/advanced/interfaces.md`

**Read when**:

- Creating ABC or Protocol classes
- Writing @abstractmethod decorators
- Designing gateway layer interfaces
- Choosing between ABC and Protocol

### `references/advanced/typing-advanced.md`

**Read when**:

- Using typing.cast()
- Creating Literal type aliases
- Narrowing types in conditional blocks

### `references/module-design.md`

**Read when**:

- Creating new Python modules
- Adding module-level code (beyond simple constants)
- Using @cache decorator at module level
- Seeing Path() or computation at module level
- Considering inline imports

### `references/advanced/api-design.md`

**Read when**:

- Adding default parameter values to functions
- Defining functions with 5 or more parameters
- Using ThreadPoolExecutor.submit()
- Reviewing function signatures

### `references/checklists.md`

**Read when**:

- Final review before committing Python code
- Unsure if you've followed all rules
- Need a quick lookup of requirements

## How to Use This Skill

1. **Core knowledge** is loaded automatically (LBYL, pathlib, basic imports, anti-patterns)
2. **Version detection** happens per package/compatibility zone - identify the minimum runtime
   Python version for the code you are editing and load the appropriate version file
3. **Reference documents** are loaded on-demand based on the triggers above
4. **Additional patterns** may require extra loading (CLI patterns, subprocess)
5. **Each file is self-contained** with complete guidance for its domain
