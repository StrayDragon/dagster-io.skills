---
---

# Type Annotations - Python 3.6

This document provides complete, canonical type annotation guidance for Python 3.6.

Python 3.6 predates modern type syntax like `X | Y` unions and `list[T]` built-in generics. In
Python 3.6 you must use `typing` imports (`Union`, `Optional`, `List`, `Dict`, ...), and you must
use **string annotations** for forward references because `from __future__ import annotations` is
not available.

## Overview

**What's new in 3.6 (typing-related):**

- Variable annotations (PEP 526): `x: int = 1`, attribute annotations, etc.

**Not available in 3.6 (upgrade required):**

- Built-in generic types: `list[str]`, `dict[str, int]` (PEP 585 is 3.9+)
- Union types with `|`: `str | int` (PEP 604 is 3.10+)
- `from __future__ import annotations` (introduced later; not in Python 3.6)

**What you need from `typing` in 3.6:**

- `List`, `Dict`, `Set`, `Tuple` for collections
- `Union`, `Optional` for sum/nullable types
- `Callable` for callables (use `typing.Callable`, not `collections.abc.Callable`)
- `TypeVar`, `Generic` for generics
- `TYPE_CHECKING` for conditional imports
- `Any` (use sparingly)

**Typing extensions (IMPORTANT):**

- Python 3.6 can only use **`typing-extensions==4.1.1`** at most.
- For `Protocol`, `TypedDict`, `Literal`, `Final`, `Annotated`, and other backports, see the
  `typing_extensions` documentation.

## Complete Type Annotation Syntax for Python 3.6

### Basic Collection Types

✅ **PREFERRED** - Use `typing` collection generics:

```python
from typing import Dict, List, Set, Tuple

names: List[str] = []
mapping: Dict[str, int] = {}
unique_ids: Set[str] = set()
coordinates: Tuple[int, int] = (0, 0)
```

❌ **WRONG** - Don't use built-in generics in Python 3.6:

```python
# Raises TypeError in Python 3.6 (built-in types aren't generic yet)
names: list[str] = []
```

### Union Types

✅ **PREFERRED** - Use `typing.Union`:

```python
from typing import Union

def process(value: Union[str, int]) -> str:
    return str(value)
```

❌ **WRONG** - Don't use `|` unions in Python 3.6:

```python
# Raises TypeError in Python 3.6 (PEP 604 is 3.10+)
def process(value: str | int) -> str:
    return str(value)
```

### Optional Types

✅ **PREFERRED** - Use `typing.Optional`:

```python
from typing import List, Optional

def first_or_none(items: List[int]) -> Optional[int]:
    return items[0] if items else None
```

❌ **WRONG** - Don't use `X | None` in Python 3.6:

```python
# Raises TypeError in Python 3.6 (PEP 604 is 3.10+)
def first_or_none(items: "List[int]") -> int | None:
    return items[0] if items else None
```

### Callable Types

✅ **PREFERRED** - Use `typing.Callable`:

```python
from typing import Callable

processor: Callable[[int], str] = str
callback: Callable[[], None] = lambda: None
```

❌ **WRONG** - Don't subscript `collections.abc.Callable` in Python 3.6:

```python
# Raises TypeError in Python 3.6
from collections.abc import Callable

processor: Callable[[int], str] = str
```

### Generic Functions with TypeVar

✅ **PREFERRED** - Use `TypeVar` + `typing` generics:

```python
from typing import List, Optional, TypeVar

T = TypeVar("T")

def first(items: List[T]) -> Optional[T]:
    return items[0] if items else None
```

### Generic Classes

✅ **PREFERRED** - Use `Generic` + `TypeVar`:

```python
from typing import Generic, List, Optional, TypeVar

T = TypeVar("T")

class Stack(Generic[T]):
    def __init__(self) -> None:
        self._items: List[T] = []

    def push(self, item: T) -> None:
        self._items.append(item)

    def pop(self) -> Optional[T]:
        if not self._items:
            return None
        return self._items.pop()
```

### Constrained and Bounded TypeVars

✅ **Use TypeVar constraints/bounds when needed**:

```python
from typing import TypeVar

Numeric = TypeVar("Numeric", int, float)

def add(a: Numeric, b: Numeric) -> Numeric:
    return a + b

class Base:
    pass

TBase = TypeVar("TBase", bound=Base)

def identity_base(obj: TBase) -> TBase:
    return obj
```

### Type Aliases

✅ **Use assignment for aliases** (no `type` statement in 3.6):

```python
from typing import Dict, List, Union

UserId = str
Config = Dict[str, Union[str, int, bool]]

JsonValue = Union[
    Dict[str, "JsonValue"],
    List["JsonValue"],
    str,
    int,
    float,
    bool,
    None,
]
```

### Forward References (No Future Annotations in 3.6)

Python 3.6 evaluates annotation expressions at runtime and **does not** support
`from __future__ import annotations`. For forward references, always use strings (and typically
`Optional[...]`):

```python
from typing import Optional

class Node:
    def __init__(self, value: int, parent: Optional["Node"] = None) -> None:
        self.value = value
        self.parent = parent
```

### Circular Imports with TYPE_CHECKING

✅ **PREFERRED** - Guard imports and use string annotations:

```python
from typing import TYPE_CHECKING, Optional

if TYPE_CHECKING:
    from other_module import Other  # Only for type checkers

class A:
    def __init__(self, other: Optional["Other"] = None) -> None:
        self.other = other
```

## Interfaces: ABC vs Protocol

✅ **PREFERRED** - Use ABC for explicit interfaces:

```python
from abc import ABC, abstractmethod
from typing import Dict, Optional

class Repository(ABC):
    @abstractmethod
    def get(self, entity_id: str) -> Optional[Dict[str, str]]:
        pass

    @abstractmethod
    def save(self, entity: Dict[str, str]) -> None:
        pass
```

🟡 **VALID** - Use `typing_extensions.Protocol` for structural typing (if needed):

```python
from typing_extensions import Protocol

class Drawable(Protocol):
    def draw(self) -> None:
        ...

def render(obj: "Drawable") -> None:
    obj.draw()
```

## Complete Examples

### Repository Pattern (In-Memory)

```python
from abc import ABC, abstractmethod
from typing import Dict, Optional

class UserRepository(ABC):
    @abstractmethod
    def get(self, user_id: str) -> Optional[Dict[str, str]]:
        pass

    @abstractmethod
    def save(self, user: Dict[str, str]) -> None:
        pass

class InMemoryUserRepository(UserRepository):
    def __init__(self) -> None:
        self._users: Dict[str, Dict[str, str]] = {}

    def get(self, user_id: str) -> Optional[Dict[str, str]]:
        return self._users.get(user_id)

    def save(self, user: Dict[str, str]) -> None:
        if "id" not in user:
            raise ValueError("User must have id")
        self._users[user["id"]] = user
```

### Generic Tree Node

```python
from typing import Callable, Generic, List, Optional, TypeVar

T = TypeVar("T")

class Node(Generic[T]):
    def __init__(self, value: T, children: Optional[List["Node[T]"]] = None) -> None:
        self.value: T = value
        self.children: List["Node[T]"] = children or []

    def add_child(self, child: "Node[T]") -> None:
        self.children.append(child)

    def find(self, predicate: Callable[[T], bool]) -> Optional["Node[T]"]:
        if predicate(self.value):
            return self
        for child in self.children:
            found = child.find(predicate)
            if found is not None:
                return found
        return None
```

### Simple Config Objects (NamedTuple)

```python
from typing import Dict, NamedTuple, Optional

class DatabaseConfig(NamedTuple):
    host: str
    port: int
    username: str
    password: Optional[str] = None

class AppConfig(NamedTuple):
    app_name: str
    debug_mode: bool
    database: DatabaseConfig
    feature_flags: Dict[str, bool]
```

## Type Checking Rules

✅ **MUST type**:

- All public function parameters (except `self`, `cls`)
- All public function return values
- All class attributes (public and private)
- Module-level constants

🟡 **SHOULD type**:

- Internal function signatures
- Complex local variables

🟢 **MAY skip**:

- Simple locals where type is obvious (`count = 0`)
- Short lambda parameters

## Migration Notes (3.6 → 3.10+)

If you can upgrade, Python 3.10+ lets you remove many `typing` imports:

- `List[T]` → `list[T]`
- `Dict[K, V]` → `dict[K, V]`
- `Union[X, Y]` → `X | Y`
- `Optional[X]` → `X | None`

## Validation

Validation in this repo was done with:

```bash
/home/l8ng/.pyenv/versions/3.6.15/bin/python
```

Notes:

- ✅ blocks were executed successfully.
- ❌ blocks were verified to raise the stated runtime error on Python 3.6 (typically `TypeError`
  for unsupported type syntax like `list[str]` or `X | Y`).
- The exact interpreter path is environment-specific; adapt it to your project.

## References

- [Python 3.6 typing module documentation](https://docs.python.org/3.6/library/typing.html)
- [PEP 484: Type Hints](https://peps.python.org/pep-0484/)
- [PEP 526: Syntax for Variable Annotations](https://peps.python.org/pep-0526/)
- [What's New In Python 3.6](https://docs.python.org/3.6/whatsnew/3.6.html)
- [typing-extensions (PyPI)](https://pypi.org/project/typing-extensions/)
