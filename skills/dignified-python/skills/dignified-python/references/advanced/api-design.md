---
description:
  Default parameter dangers, keyword-only arguments, ThreadPoolExecutor patterns, speculative test
  infrastructure.
---

# API Design Reference

**Read when**: Adding default parameters, functions with 5+ params, using ThreadPoolExecutor

---

**Version Note**: Examples in this document default to Python 3.10+ type syntax. If you are editing
Python 3.6-3.9 compatible code, apply the same design rules but translate type syntax per
`versions/python-3.6.md` (e.g., `dict[str, str]` → `Dict[str, str]`, `X | None` → `Optional[X]`).

Some code blocks below are illustrative fragments and may omit app-specific stubs. For a
copy-paste runnable example, see **Copy-Paste Runnable Demo (Python 3.6+)** at the end.

---

## Default Parameter Values Are Dangerous

> **Scope:** This rule applies to **function definitions** (`def foo(bar: bool = False)`), NOT to
> **function calls** where you pass an argument named `default` (e.g.,
> `click.confirm(default=True)`). Passing `default=True` to a function that accepts a `default`
> parameter is perfectly valid—you're not creating a default parameter value, you're explicitly
> providing a value.

**Avoid default parameter values unless absolutely necessary.** They are a significant source of
bugs.

**Why defaults are dangerous:**

1. **Silent incorrect behavior** - Callers forget to pass a parameter and get unexpected results
2. **Hidden coupling** - The default encodes an assumption that may not hold for all callers
3. **Audit difficulty** - Hard to verify all call sites are using the right value
4. **Refactoring hazard** - Adding a new parameter with a default doesn't trigger errors at existing
   call sites

```python
# DANGEROUS: Default that might be wrong for some callers
def process_file(path: Path, encoding: str = "utf-8") -> str:
    return path.read_text(encoding=encoding)

# Caller forgets encoding, silently gets wrong behavior for legacy file
content = process_file(legacy_latin1_file)  # Bug: should be encoding="latin-1"

# SAFER: Require explicit choice
def process_file(path: Path, encoding: str) -> str:
    return path.read_text(encoding=encoding)

# Caller must think about encoding
content = process_file(legacy_latin1_file, encoding="latin-1")
```

**When you discover a default is never overridden, eliminate it:**

```python
# If every call site uses the default...
sync_repo(repo_path, mode="up", preserve_relative_paths=True)  # Always True
sync_repo(repo_path, mode="down", preserve_relative_paths=True)  # Always True

# CORRECT: Remove the parameter entirely
def sync_repo(repo_path: Path, mode: str) -> None:
    # Always preserve relative paths - it's just the behavior
    ...
```

**Acceptable uses of defaults:**

1. **Truly optional behavior** - Where the default is correct for 95%+ of callers
2. **Backwards compatibility** - When adding a parameter to existing API (temporary)
3. **Test helper functions** - Functions in `tests/test_utils/` that exist to reduce test
   boilerplate are explicitly exempt. These helpers often wrap complex constructors (like
   `format_plan_header_body`) with sensible defaults, and having many default parameters is their
   intended purpose—not a code smell

**When reviewing code with defaults, ask:**

- Do all call sites actually want this default?
- Would a caller forgetting this parameter cause a bug?
- Is there a safer design that makes the choice explicit?

---

## Keyword-Only Arguments for Complex Functions

**Functions with 5 or more parameters MUST use keyword-only arguments.**

Use the `*` separator after the first positional parameter to enforce keyword-only at the language
level. This improves call-site readability by forcing explicit parameter names.

**Python 3.10+**

```python
# CORRECT: Keyword-only after first param
def fetch_data(
    url,
    *,
    timeout: float,
    retries: int,
    headers: dict[str, str],
    auth_token: str,
) -> Response:
    ...

# Call site is self-documenting
response = fetch_data(
    api_url,
    timeout=30.0,
    retries=3,
    headers={"Accept": "application/json"},
    auth_token=token,
)

# WRONG: All positional parameters
def fetch_data(
    url,
    timeout: float,
    retries: int,
    headers: dict[str, str],
    auth_token: str,
) -> Response:
    ...

# Call site is unreadable - what do these values mean?
response = fetch_data(api_url, 30.0, 3, {"Accept": "application/json"}, token)
```

**Python 3.6-3.9 compatible**

```python
from typing import Dict

def fetch_data(
    url,
    *,
    timeout: float,
    retries: int,
    headers: Dict[str, str],
    auth_token: str,
) -> Response:
    ...

response = fetch_data(
    api_url,
    timeout=30.0,
    retries=3,
    headers={"Accept": "application/json"},
    auth_token=token,
)

def fetch_data(
    url,
    timeout: float,
    retries: int,
    headers: Dict[str, str],
    auth_token: str,
) -> Response:
    ...

response = fetch_data(api_url, 30.0, 3, {"Accept": "application/json"}, token)
```

**Exceptions:**

1. **`self`** - Always positional (Python requirement)
2. **`ctx` / context objects** - Can remain positional as the first parameter (convention)
3. **ABC/Protocol methods** - Exempt to avoid forcing all implementations to change signatures
4. **Click callbacks** - Click injects parameters; follow Click conventions

```python
# CORRECT: ctx stays positional, rest are keyword-only
def create_project(
    ctx: object,
    *,
    name: str,
    template: str,
    path: Path,
    init_git: bool,
) -> "ProjectInfo":
    ...
```

---

## ThreadPoolExecutor.submit() Pattern

`ThreadPoolExecutor.submit()` passes arguments positionally to the callable. For functions with
keyword-only parameters, wrap the call in a lambda:

```python
# WRONG: submit() passes args positionally - fails with keyword-only functions
future = executor.submit(fetch_data, url, timeout, retries, headers, token)

# CORRECT: Lambda enables keyword arguments
future = executor.submit(
    lambda: fetch_data(
        url,
        timeout=timeout,
        retries=retries,
        headers=headers,
        auth_token=token,
    )
)
```

---

## Speculative Test Infrastructure

**Don't add parameters to fakes "just in case" they might be useful for testing.**

Fakes should mirror production interfaces. Adding test-only configuration knobs that never get used
creates dead code and false complexity.

**Python 3.10+**

```python
# WRONG: Test-only parameter that's never used in production
class FakeGitHub:
    def __init__(
        self,
        prs: dict[str, PullRequestInfo] | None = None,
        rate_limited: bool = False,  # "Might test this later"
    ) -> None:
        self._rate_limited = rate_limited  # Never set to True anywhere

# CORRECT: Only add infrastructure when you need it
class FakeGitHub:
    def __init__(
        self,
        prs: dict[str, PullRequestInfo] | None = None,
    ) -> None:
        ...
```

**Python 3.6-3.9 compatible**

```python
from typing import Dict, Optional

class FakeGitHub:
    def __init__(
        self,
        prs: Optional[Dict[str, "PullRequestInfo"]] = None,
        rate_limited: bool = False,
    ) -> None:
        self._rate_limited = rate_limited

class FakeGitHub:
    def __init__(
        self,
        prs: Optional[Dict[str, "PullRequestInfo"]] = None,
    ) -> None:
        ...
```

**The test for this:** If grep shows a parameter is only ever passed in test files, and those tests
are testing hypothetical scenarios rather than actual production behavior, delete both the parameter
and the tests.

---

## Speculative Tests

```python
# FORBIDDEN: Tests for future features
# def test_feature_we_might_add():
#     pass

# CORRECT: TDD for current implementation
def test_feature_being_built_now():
    result = new_feature()
    assert result == expected
```

---

## Decision Checklist

Before adding a default parameter value:

- [ ] Do 95%+ of callers actually want this default?
- [ ] Would forgetting to pass this parameter cause a subtle bug?
- [ ] Is there a safer design that makes the choice explicit?
- [ ] If the default is never overridden anywhere, should this parameter exist at all?

**Default: Require explicit values; eliminate unused defaults**

Before adding a function with 5+ parameters:

- [ ] Have I added `*` after the first (or ctx) parameter?
- [ ] Is only `self`/`ctx` positional?
- [ ] Is this an ABC/Protocol method? (exempt from rule)
- [ ] If using ThreadPoolExecutor.submit(), am I using a lambda wrapper?

**Default: All parameters after the first should be keyword-only**

---

## Copy-Paste Runnable Demo (Python 3.6+)

```python
from concurrent.futures import ThreadPoolExecutor
from typing import List

def dangerous_default(x: int, history: List[int] = []) -> List[int]:
    # Mutable defaults are the classic footgun: the same list is shared across calls.
    history.append(x)
    return history

def fetch_data(
    url: str,
    *,
    timeout: float,
    retries: int,
) -> str:
    # Demo stub (no network)
    return "url=%s timeout=%s retries=%s" % (url, timeout, retries)

if __name__ == "__main__":
    print(dangerous_default(1))
    print(dangerous_default(2))  # Surprise: [1, 2]

    with ThreadPoolExecutor(max_workers=1) as executor:
        # WRONG: submit() passes args positionally -> TypeError for keyword-only params
        bad_future = executor.submit(fetch_data, "https://example.com", 1.0, 3)
        try:
            bad_future.result()
        except TypeError as e:
            print("expected TypeError:", e)

        # CORRECT: wrap in lambda to use keywords
        ok_future = executor.submit(
            lambda: fetch_data("https://example.com", timeout=1.0, retries=3)
        )
        print(ok_future.result())
```
