# Compound Statements

Compound statements contain other statements — they're the building blocks of control flow, error handling, and code organization. Each compound statement has a **header** line ending in `:` and a **body** (one or more indented statements).

---

## `if` / `elif` / `else`

```python
x = 42

if x > 100:
    print("large")
elif x > 50:
    print("medium")
elif x > 0:
    print("small")
else:
    print("zero or negative")
```

- `elif` is short for "else if" — avoids deep nesting.
- `else` is optional.
- Python has no `switch` statement — use `match` (3.10+) or a dict for dispatch.

---

## `while`

Repeats the body as long as the condition is true:

```python
n = 10
total = 0
while n > 0:
    total += n
    n -= 1
print(total)   # 55

# Infinite loop with break
import time
while True:
    task = get_next_task()
    if task is None:
        break
    process(task)
```

### `while` / `else`

The `else` clause runs if the loop completed *without* hitting a `break`:

```python
target = 7
for n in range(10):
    if n == target:
        print(f"Found {target}")
        break
else:
    print(f"{target} not found in range")  # runs if no break
```

---

## `for`

Iterates over any iterable:

```python
# Over a list
for fruit in ["apple", "banana", "cherry"]:
    print(fruit)

# Over a range
for i in range(5):         # 0, 1, 2, 3, 4
    print(i)

for i in range(2, 10, 2): # 2, 4, 6, 8
    print(i)

# Over a dict (iterates over keys by default)
d = {"a": 1, "b": 2, "c": 3}
for key in d:
    print(key, d[key])

# Over key-value pairs
for key, value in d.items():
    print(f"{key} = {value}")

# Enumerate — index + value
for i, fruit in enumerate(["apple", "banana"]):
    print(i, fruit)  # 0 apple / 1 banana
```

---

## `try` / `except` / `else` / `finally`

The full exception handling syntax:

```python
try:
    result = 10 / int(input("Enter denominator: "))
except ZeroDivisionError:
    print("Can't divide by zero!")
except ValueError as e:
    print(f"Not a number: {e}")
except (TypeError, AttributeError) as e:
    print(f"Type problem: {e}")
except Exception as e:
    print(f"Unexpected error: {e}")
    raise   # re-raise
else:
    # Runs ONLY if no exception was raised
    print(f"Result: {result}")
finally:
    # Runs ALWAYS — with or without exception
    print("Cleanup done.")
```

### Exception Groups (`except*`) — Python 3.11+

For handling multiple concurrent exceptions (see [What's New in 3.11](../whats-new/3.11.md)):

```python
try:
    raise ExceptionGroup("errors", [ValueError("v"), TypeError("t")])
except* ValueError as eg:
    print("Handled ValueError(s)")
except* TypeError as eg:
    print("Handled TypeError(s)")
```

---

## `with` — Context Managers

Guarantees that `__enter__` and `__exit__` are called (like try/finally, but cleaner):

```python
# Classic use: file handling
with open("data.txt", "r") as f:
    content = f.read()
# f.close() is automatically called, even if an exception occurred

# Multiple context managers (Python 3.10+: parenthesized style)
with (
    open("input.txt") as fin,
    open("output.txt", "w") as fout,
):
    fout.write(fin.read())

# Suppressing exceptions
from contextlib import suppress
with suppress(FileNotFoundError):
    os.remove("maybe_exists.tmp")
```

---

## `match` / `case` — Python 3.10+

Structural pattern matching. Much more powerful than a simple switch:

```python
def handle_command(command):
    match command.split():
        case ["quit"]:
            return "quit"
        case ["go", direction] if direction in ("north", "south", "east", "west"):
            return f"going {direction}"
        case ["pick", "up", item]:
            return f"picking up {item}"
        case _:
            return f"unknown: {command!r}"
```

Pattern types:

| Pattern | Matches |
|---------|---------|
| `42` | Literal value `42` |
| `True`, `False`, `None` | Singletons |
| `[a, b, c]` | Sequence with exactly 3 elements |
| `[first, *rest]` | Sequence with at least 1 element |
| `{"key": value}` | Mapping with at least this key |
| `Point(x=0, y=y)` | Instance with these attributes |
| `x` | Any value, bound to name `x` |
| `_` | Any value, not bound |
| `A \| B` | Either pattern A or B |
| `pattern if condition` | Pattern with guard |

---

## Function Definitions (`def`)

```python
def greet(name: str, greeting: str = "Hello") -> str:
    """
    Returns a greeting string.
    
    Args:
        name: The person to greet.
        greeting: The greeting word. Defaults to "Hello".
    
    Returns:
        A formatted greeting string.
    """
    return f"{greeting}, {name}!"
```

- The docstring (first string literal in the body) is stored as `greet.__doc__`.
- Type annotations are stored in `greet.__annotations__`.
- Default values are evaluated once at definition time (see [Functions](../fundamentals/functions.md)).

### `async def`

Defines a coroutine function (see [Asyncio](../advanced/asyncio-concurrency.md)):

```python
import asyncio

async def fetch(url: str) -> str:
    async with aiohttp.ClientSession() as session:
        async with session.get(url) as response:
            return await response.text()
```

---

## Class Definitions (`class`)

```python
class Animal:
    """Base class for all animals."""
    
    kingdom = "Animalia"   # class variable
    
    def __init__(self, name: str, sound: str):
        self.name = name   # instance variable
        self.sound = sound
    
    def speak(self) -> str:
        return f"{self.name} says {self.sound}"
    
    @classmethod
    def describe(cls) -> str:
        return f"I am a {cls.__name__} from {cls.kingdom}"
    
    @staticmethod
    def breathes() -> bool:
        return True

class Dog(Animal):
    def __init__(self, name: str):
        super().__init__(name, "woof")
    
    def fetch(self, item: str) -> str:
        return f"{self.name} fetches the {item}!"
```

See the full [OOP guide](../core/oop.md) for inheritance, metaclasses, and advanced class mechanics.
