# Modern Type Annotations

Python is dynamically typed, meaning variable types are verified at runtime. Starting with Python 3.5 (PEP 484), Python introduced optional **type hints** (type annotations) to document function signatures, enable IDE autocompletion, and allow static analysis tools (such as Mypy) to catch bugs before execution.

---

## Basic Type Annotations

```python
# Variables
name: str = "Alice"
age: int = 30
is_active: bool = True
price: float = 19.99

# Function parameters and return value
def calculate_total(subtotal: float, tax_rate: float) -> float:
    return subtotal * (1 + tax_rate)
```

---

## Built-In Generic Types (Python 3.9+)

Starting with Python 3.9, built-in collection types (`list`, `dict`, `set`, `tuple`) can be parameterized directly without importing from the `typing` module:

<div class="example-box">
<div class="example-title">Example: Modern Collection Types</div>

```python
def get_user_scores(scores: dict[str, list[int]]) -> dict[str, float]:
    return {user: sum(vals) / len(vals) for user, vals in scores.items()}

sample = {"Alice": [90, 85, 95], "Bob": [70, 75, 80]}
print(get_user_scores(sample))
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
{'Alice': 90.0, 'Bob': 75.0}
</div>
</div>

---

## The Union Operator `|` (Python 3.10+)

Use the pipe operator `|` to represent multiple acceptable types:

```python
# Accepts either an int or a float
def process_number(x: int | float) -> str:
    return f"Value: {x}"

# Optional type (type or None)
def find_record(record_id: int) -> dict[str, str] | None:
    if record_id == 1:
        return {"name": "Admin"}
    return None
```

---

## Specific Types: `Literal`, `TypedDict`, and `Callable`

### `Literal`: Specific Allowed Values
```python
from typing import Literal

def set_mode(mode: Literal["read", "write", "append"]):
    print("Mode set to:", mode)

set_mode("read") # Valid
```

### `TypedDict`: Dictionary with Fixed Key Types
```python
from typing import TypedDict

class UserProfile(TypedDict):
    id: int
    username: str
    is_staff: bool

user: UserProfile = {"id": 1, "username": "admin", "is_staff": True}
```

### `Callable`: Function Types
```python
from typing import Callable

def apply_func(x: int, func: Callable[[int], int]) -> int:
    return func(x)

print(apply_func(5, lambda n: n * 2))  # 10
```

---

## Static Type Checking with Mypy

To run static type checking across your project, install and run Mypy:

```bash
pip install mypy
mypy script.py
```
