# `typing` — Support for Type Hints

Python remains a dynamically typed language: types are verified at runtime when operations execute, not beforehand by the interpreter. However, the `typing` module (introduced in Python 3.5 with PEP 484) provides a comprehensive vocabulary for declaring static type annotations.

These annotations are inspected by static type checkers (like **mypy**, **pyright**, and **Pyre**), modern IDEs for code completion and refactoring, and runtime libraries (like **Pydantic** and **FastAPI**).

---

## Evolution of Python Typing Syntax

Python's typing syntax has evolved dramatically across recent releases:

| Feature | Legacy (Python 3.5 - 3.8) | Modern (Python 3.9 - 3.11) | State of the Art (Python 3.12+) |
| :--- | :--- | :--- | :--- |
| **Collections** | `typing.List[int]`, `Dict[str, int]` | `list[int]`, `dict[str, int]` (PEP 585) | `list[int]`, `dict[str, int]` |
| **Unions / Optionals** | `Union[int, str]`, `Optional[int]` | `int | str`, `int | None` (PEP 604) | `int | str`, `int | None` |
| **Type Aliases** | `Vector = List[float]` | `Vector: TypeAlias = list[float]` | `type Vector = list[float]` (PEP 695) |
| **Generics** | `T = TypeVar('T')` <br> `class Box(Generic[T])` | `T = TypeVar('T')` <br> `class Box(Generic[T])` | `class Box[T]:` (PEP 695) |

---

## Modern Generics and Built-in Collections (PEP 585)

Starting in Python 3.9, standard collection types can be parameterized directly without importing from `typing`:

```python
# Modern Python 3.9+
users: list[str] = ["Alice", "Bob"]
user_scores: dict[str, float] = {"Alice": 98.5, "Bob": 84.0}
unique_ids: set[int] = {101, 102, 103}
point_2d: tuple[int, int] = (10, 20)
arbitrary_ints: tuple[int, ...] = (1, 2, 3, 4, 5)
```

---

## Unions and Optionals (PEP 604)

Starting in Python 3.10, use the pipe `|` operator for union types and nullable values:

```python
# Accepts either an int or a float
def calculate_tax(amount: int | float) -> float:
    return amount * 0.085

# Accepts a string or None (Optional)
def find_user_by_email(email: str) -> dict[str, str] | None:
    if email == "admin@example.com":
        return {"name": "Admin"}
    return None
```

---

## Generic Syntax in Python 3.12+ (PEP 695)

Python 3.12 introduced the `type` statement and native square-bracket syntax for defining generic functions and classes, eliminating the need for `TypeVar` and `Generic`:

=== "Python 3.12+ (PEP 695)"
    ```python
    # Generic function
    def first[T](items: list[T]) -> T | None:
        return items[0] if items else None

    # Generic class
    class Stack[T]:
        def __init__(self) -> None:
            self._items: list[T] = []

        def push(self, item: T) -> None:
            self._items.append(item)

        def pop(self) -> T:
            return self._items.pop()

    # Type alias statement
    type Coordinate2D = tuple[float, float]
    type Matrix[T] = list[list[T]]
    ```

=== "Python 3.11 and Earlier"
    ```python
    from typing import TypeVar, Generic

    T = TypeVar("T")

    def first(items: list[T]) -> T | None:
        return items[0] if items else None

    class Stack(Generic[T]):
        def __init__(self) -> None:
            self._items: list[T] = []

        def push(self, item: T) -> None:
            self._items.append(item)

        def pop(self) -> T:
            return self._items.pop()

    Coordinate2D = tuple[float, float]
    ```

---

## Structural Subtyping with `Protocol` (Duck Typing)

By default, Python subclassing uses nominal subtyping (`isinstance(Dog, Animal)`). The `Protocol` class enables **structural subtyping** — matching any class that implements the required methods and attributes regardless of inheritance:

```python
from typing import Protocol

class Renderable(Protocol):
    def render(self) -> str:
        ...

# Classes do NOT need to inherit from Renderable!
class Button:
    def render(self) -> str:
        return "<button>Click Me</button>"

class MarkdownDocument:
    def render(self) -> str:
        return "# Heading\nDocument body."

def display_ui(component: Renderable) -> None:
    print(component.render())

# Both work cleanly and pass type checkers:
display_ui(Button())
display_ui(MarkdownDocument())
```

---

## Structured Dictionaries with `TypedDict`

A `TypedDict` declares type requirements for specific keys in a standard dictionary, without creating a class or tuple wrapper:

```python
from typing import TypedDict, NotRequired

class DatabaseConfig(TypedDict):
    host: str
    port: int
    database: str
    ssl_enabled: bool
    password: NotRequired[str]  # Optional field

config: DatabaseConfig = {
    "host": "localhost",
    "port": 5432,
    "database": "production",
    "ssl_enabled": True
}
```

---

## Special Type Qualifiers

### `Literal` — Restricting Permitted Values

Ensures a variable can only equal specific enumerated values:

```python
from typing import Literal

def set_log_level(level: Literal["DEBUG", "INFO", "WARNING", "ERROR"]) -> None:
    print(f"Log level set to {level}")

set_log_level("INFO")       # OK
# set_log_level("VERBOSE")  # Type checker error!
```

### `Self` — Method Fluent Returns (Python 3.11+)

Annotations for methods that return an instance of their own class (useful for builder patterns and method chaining):

```python
from typing import Self

class QueryBuilder:
    def __init__(self) -> None:
        self.query = ""

    def select(self, fields: str) -> Self:
        self.query += f"SELECT {fields} "
        return self

    def from_table(self, table: str) -> Self:
        self.query += f"FROM {table} "
        return self

qb = QueryBuilder().select("*").from_table("users")
```

### `Final` and `ClassVar`

```python
from typing import Final, ClassVar

# Constant that cannot be reassigned
MAX_CONNECTIONS: Final[int] = 100

class User:
    # Belongs to the class, not individual instances
    table_name: ClassVar[str] = "users"

    def __init__(self, name: str) -> None:
        self.name: str = name
```

---

## Runtime Type Checking vs Static Type Checking

Remember: **Python does not enforce type annotations at runtime**.

```python
def add(a: int, b: int) -> int:
    return a + b

# Python executes this without error at runtime!
result = add("hello", "world")
print(result)  # "helloworld"
```

To catch type errors before deployment, run static analysis tools in CI/CD:

```bash
# Run mypy on your codebase
pip install mypy
mypy src/
```
