---
---

# Subprocess Handling - Safe Execution

## Version Note

The subprocess rules here apply across Python versions, but the **type syntax** in examples must be
adjusted to your project's minimum runtime version (see `SKILL.md` version detection rules).

- Python 3.10+ examples use modern syntax (`list[str]`, `X | None`).
- Python 3.6-3.9 compatible code should follow `versions/python-3.6.md` (e.g., `List[str]`,
  `Optional[X]`).

```python
from pathlib import Path

# 3.10+
def run_git_command(args: list[str], cwd: Path | None = None) -> str: ...

# 3.6-3.9
from typing import List, Optional

def run_git_command(args: List[str], cwd: Optional[Path] = None) -> str: ...
```

## Core Rule

**ALWAYS use `check=True` with `subprocess.run()`**

## Basic Subprocess Pattern

**Python 3.10+**

```python
import subprocess
from pathlib import Path

# ✅ CORRECT: check=True to raise on error
result = subprocess.run(
    ["git", "status"],
    check=True,
    capture_output=True,
    text=True
)
print(result.stdout)

# ❌ WRONG: No check - silently ignores errors
result = subprocess.run(["git", "status"])
```

**Python 3.6-3.9 compatible**

```python
import subprocess

# ✅ CORRECT: check=True to raise on error
result = subprocess.run(
    ["git", "status"],
    check=True,
    stdout=subprocess.PIPE,
    stderr=subprocess.PIPE,
    universal_newlines=True,  # 3.6 replacement for text=True
)
print(result.stdout)

# ❌ WRONG: No check - silently ignores errors
result = subprocess.run(["git", "status"])
```

## Complete Subprocess Example

**Python 3.10+**

```python
import subprocess
from pathlib import Path

def run_git_command(args: list[str], cwd: Path | None = None) -> str:
    """Run a git command and return output."""
    try:
        result = subprocess.run(
            ["git"] + args,
            check=True,          # Raise on non-zero exit
            capture_output=True, # Capture stdout/stderr
            text=True,          # Return strings, not bytes
            cwd=cwd            # Working directory
        )
        return result.stdout.strip()
    except subprocess.CalledProcessError as e:
        # Error boundary - add context
        raise RuntimeError(f"Git command failed: {e.stderr}") from e
```

**Python 3.6-3.9 compatible**

```python
import subprocess
from pathlib import Path
from typing import List, Optional

def run_git_command(args: List[str], cwd: Optional[Path] = None) -> str:
    """Run a git command and return output."""
    try:
        result = subprocess.run(
            ["git"] + args,
            check=True,                  # Raise on non-zero exit
            stdout=subprocess.PIPE,      # 3.6 replacement for capture_output=True
            stderr=subprocess.PIPE,
            universal_newlines=True,     # 3.6 replacement for text=True
            cwd=cwd,                     # Working directory
        )
        return result.stdout.strip()
    except subprocess.CalledProcessError as e:
        # Error boundary - add context
        raise RuntimeError("Git command failed: %s" % (e.stderr,)) from e
```

## Error Handling

**Python 3.10+**

```python
try:
    result = subprocess.run(
        ["make", "test"],
        check=True,
        capture_output=True,
        text=True
    )
except subprocess.CalledProcessError as e:
    # Access error details
    print(f"Command: {e.cmd}")
    print(f"Exit code: {e.returncode}")
    print(f"Stdout: {e.stdout}")
    print(f"Stderr: {e.stderr}")
    raise
```

**Python 3.6-3.9 compatible**

```python
import subprocess

try:
    result = subprocess.run(
        ["make", "test"],
        check=True,
        stdout=subprocess.PIPE,
        stderr=subprocess.PIPE,
        universal_newlines=True,
    )
except subprocess.CalledProcessError as e:
    print("Command: %s" % (e.cmd,))
    print("Exit code: %s" % (e.returncode,))
    print("Stdout: %s" % (e.stdout,))
    print("Stderr: %s" % (e.stderr,))
    raise
```

## Common Patterns

**Python 3.10+**

```python
# Silent execution (no output)
subprocess.run(["git", "fetch"], check=True, capture_output=True)

# Stream output in real-time
process = subprocess.Popen(
    ["pytest", "-v"],
    stdout=subprocess.PIPE,
    stderr=subprocess.STDOUT,
    text=True
)
for line in process.stdout:
    print(line, end="")
process.wait()
if process.returncode != 0:
    raise subprocess.CalledProcessError(process.returncode, process.args)

# With timeout
try:
    subprocess.run(["long-command"], check=True, timeout=30)
except subprocess.TimeoutExpired:
    print("Command timed out")
```

**Python 3.6-3.9 compatible**

```python
import subprocess

# Silent execution (no output)
subprocess.run(
    ["git", "fetch"],
    check=True,
    stdout=subprocess.PIPE,
    stderr=subprocess.PIPE,
    universal_newlines=True,
)

# Stream output in real-time
process = subprocess.Popen(
    ["pytest", "-v"],
    stdout=subprocess.PIPE,
    stderr=subprocess.STDOUT,
    universal_newlines=True,
)
for line in process.stdout:
    print(line, end="")
process.wait()
if process.returncode != 0:
    raise subprocess.CalledProcessError(process.returncode, process.args)

# With timeout
try:
    subprocess.run(["long-command"], check=True, timeout=30)
except subprocess.TimeoutExpired:
    print("Command timed out")
```

## Key Takeaways

1. **Always check=True**: Ensure errors are not silently ignored
2. **Capture output**: Use `capture_output=True` (3.7+) or `stdout=PIPE, stderr=PIPE` (3.6)
3. **Text mode**: Use `text=True` (3.7+) or `universal_newlines=True` (3.6)
4. **Error context**: Wrap in try/except at boundaries
5. **Timeout safety**: Set timeout for long-running commands
