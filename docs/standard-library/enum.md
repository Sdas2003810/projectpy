# `enum` — Support for Enumerations

An enumeration is a set of symbolic names (members) bound to unique, constant values. Using raw strings (`"pending"`, `"active"`) or arbitrary integers (`0`, `1`, `2`) for state flags leads to subtle bugs: typos in strings pass silently, magic integers lack meaning, and invalid values cannot be prevented.

The standard library `enum` module (PEP 435) provides strongly-typed enumerations that eliminate these failure modes.

---

## Basic Enumeration with `Enum`

Define an enumeration by subclassing `Enum`:

```python
from enum import Enum

class OrderStatus(Enum):
    PENDING = "pending"
    PROCESSING = "processing"
    SHIPPED = "shipped"
    DELIVERED = "delivered"
    CANCELLED = "cancelled"

# Access members
status = OrderStatus.PENDING

# Inspect name and value
print(status.name)   # "PENDING"
print(status.value)  # "pending"
```

### Identity and Equality

Enum members are singletons. Use `is` or `==` for comparisons:

```python
# Both identity and equality checks work:
if status is OrderStatus.PENDING:
    print("Order is awaiting payment confirmation.")

# Member identity is unique:
print(OrderStatus.PENDING == OrderStatus.SHIPPED)  # False
```

---

## Automatic Values with `auto()`

When the specific values do not matter (only distinct symbolic identities are required), use `auto()` to assign sequential values automatically:

```python
from enum import Enum, auto

class Direction(Enum):
    NORTH = auto()  # 1
    EAST = auto()   # 2
    SOUTH = auto()  # 3
    WEST = auto()   # 4

print(Direction.NORTH.value)  # 1
```

---

## Enforcing Uniqueness with `@unique`

By default, Python allows multiple enum member names to share the same value (creating aliases). To disallow aliases and raise `ValueError` on duplicates, decorate with `@enum.unique`:

```python
from enum import Enum, unique

@unique
class HttpCode(Enum):
    OK = 200
    NOT_FOUND = 404
    SERVER_ERROR = 500
    # Duplicate value raises ValueError:
    # BAD_GATEWAY = 500
```

---

## Specialized Enum Subclasses

### 1. `StrEnum` (Python 3.11+)

`StrEnum` members inherit directly from `str`. They can be used directly wherever a string is expected, making them ideal for JSON serialization and API schemas:

```python
from enum import StrEnum

class FileFormat(StrEnum):
    JSON = "json"
    CSV = "csv"
    PARQUET = "parquet"

# Directly usable in string operations
print(f"Exporting to {FileFormat.CSV} format.")
print(FileFormat.JSON.endswith("on"))  # True
```

### 2. `IntEnum`

`IntEnum` members inherit from `int` and can be compared directly with integers:

```python
from enum import IntEnum

class Priority(IntEnum):
    LOW = 1
    MEDIUM = 2
    HIGH = 3

print(Priority.HIGH > Priority.LOW)  # True
print(Priority.HIGH == 3)            # True (compares with raw int)
```

### 3. Bitwise Flags with `Flag` and `IntFlag`

`Flag` supports bitwise operations (`|`, `&`, `~`) to represent combinations of permissions or options:

```python
from enum import Flag, auto

class Permission(Flag):
    READ = auto()     # 1 (0b001)
    WRITE = auto()    # 2 (0b010)
    EXECUTE = auto()  # 4 (0b100)

# Combine flags with bitwise OR
user_perms = Permission.READ | Permission.WRITE

# Check if a specific permission is granted
print(Permission.READ in user_perms)     # True
print(Permission.EXECUTE in user_perms)  # False
print(repr(user_perms))                  # <Permission.READ|WRITE: 3>
```

---

## Accessing Members Dynamically

Convert between raw values/strings and Enum members:

```python
# Lookup by name (string):
status = OrderStatus["SHIPPED"]
print(status)  # OrderStatus.SHIPPED

# Lookup by value:
status_by_val = OrderStatus("delivered")
print(status_by_val)  # OrderStatus.DELIVERED

# Safe lookup with fallback:
try:
    invalid = OrderStatus("unknown")
except ValueError:
    print("Invalid order status supplied!")
```

---

## Adding Methods to Enums

Enums are full Python classes and can define methods and properties:

```python
class Mood(Enum):
    HAPPY = 1
    TIRED = 2
    ENERGIZED = 3

    def describe(self) -> str:
        descriptions = {
            Mood.HAPPY: "Feeling cheerful and productive!",
            Mood.TIRED: "Needs a fresh cup of coffee.",
            Mood.ENERGIZED: "Ready to conquer the backlog!"
        }
        return descriptions[self]

print(Mood.HAPPY.describe())
```

<div class="terminal-output">
Feeling cheerful and productive!
</div>
