# `dataclasses` — Data Classes

Introduced in **Python 3.7 (PEP 557)**, the `dataclasses` module provides a decorator and functions to automatically generate special methods (such as `__init__()`, `__repr__()`, and `__eq__()`) on user-defined classes based on type annotations.

---

## Why Dataclasses?

Writing plain classes that primarily hold data involves immense boilerplate:

=== "With `@dataclass`"
    ```python
    from dataclasses import dataclass

    @dataclass
    class InventoryItem:
        name: str
        unit_price: float
        quantity_on_hand: int = 0

        def total_value(self) -> float:
            return self.unit_price * self.quantity_on_hand
    ```

=== "Without `@dataclass` (Boilerplate)"
    ```python
    class InventoryItem:
        def __init__(self, name: str, unit_price: float, quantity_on_hand: int = 0):
            self.name = name
            self.unit_price = unit_price
            self.quantity_on_hand = quantity_on_hand

        def __repr__(self) -> str:
            return (f"InventoryItem(name={self.name!r}, "
                    f"unit_price={self.unit_price!r}, "
                    f"quantity_on_hand={self.quantity_on_hand!r})")

        def __eq__(self, other) -> bool:
            if other.__class__ is not self.__class__:
                return NotImplemented
            return (self.name, self.unit_price, self.quantity_on_hand) == \
                   (other.name, other.unit_price, other.quantity_on_hand)
    ```

The `@dataclass` version automatically generates identical `__init__`, clean string representations in `__repr__`, and structural value comparison in `__eq__`.

```python
item1 = InventoryItem("Mechanical Keyboard", 89.99, 10)
item2 = InventoryItem("Mechanical Keyboard", 89.99, 10)

print(item1)               # InventoryItem(name='Mechanical Keyboard', unit_price=89.99, quantity_on_hand=10)
print(item1 == item2)      # True
print(item1.total_value()) # 899.90
```

---

## Customizing Fields with `field()`

The `field()` function configures specific behavior for individual attributes:

```python
from dataclasses import dataclass, field
import uuid

@dataclass
class UserAccount:
    username: str
    email: str
    # Generate unique ID dynamically for each instance
    user_id: str = field(default_factory=lambda: str(uuid.uuid4()))
    # Mutable default must use default_factory!
    roles: list[str] = field(default_factory=list)
    # Exclude sensitive fields from repr and comparison
    password_hash: str = field(repr=False, compare=False, default="")
```

### The Mutable Default Gotcha

!!! danger "Never Use `[]` or `{}` as Default Values!"
    In Python, default arguments are shared across all instances. Dataclasses explicitly protect you by throwing a `ValueError`:
    ```python
    # Raises ValueError: mutable default [list] is not allowed: use default_factory
    # @dataclass
    # class Bad:
    #     items: list = []
    ```
    Always use `field(default_factory=list)` or `field(default_factory=dict)`.

---

## Post-Initialization with `__post_init__`

If you need validation, computed properties, or initialization logic after `__init__` sets fields, define `__post_init__()`:

```python
@dataclass
class Rectangle:
    width: float
    height: float
    area: float = field(init=False)

    def __post_init__(self):
        if self.width <= 0 or self.height <= 0:
            raise ValueError("Dimensions must be positive")
        self.area = self.width * self.height

rect = Rectangle(4.0, 5.0)
print(rect.area)  # 20.0
```

---

## Immutable Dataclasses: `frozen=True`

Setting `frozen=True` makes instances immutable and automatically generates `__hash__()`, allowing them to be used as dictionary keys or stored in sets:

```python
@dataclass(frozen=True)
class Coordinate:
    latitude: float
    longitude: float

coord = Coordinate(37.7749, -122.4194)

# Attempting to reassign raises FrozenInstanceError
# coord.latitude = 40.0  # dataclasses.FrozenInstanceError

# Can be used in sets and as dict keys:
geo_cache = {coord: "San Francisco"}
print(geo_cache[coord])
```

---

## High-Performance Dataclasses: `slots=True` (Python 3.10+)

In Python 3.10+, you can pass `slots=True` to create classes that store attributes in `__slots__` instead of an instance `__dict__`. This reduces memory footprint by up to 60% and speeds up attribute access:

```python
@dataclass(slots=True)
class FastPoint:
    x: float
    y: float
    z: float
```

---

## Conversion and Copy Helpers

The `dataclasses` module includes built-in functions for serialization and transformation:

```python
from dataclasses import asdict, astuple, replace

item = InventoryItem("Monitor", 299.99, 5)

# Convert to nested dictionary (ideal for JSON export)
item_dict = asdict(item)
print(item_dict)
# {'name': 'Monitor', 'unit_price': 299.99, 'quantity_on_hand': 5}

# Convert to tuple
item_tuple = astuple(item)
print(item_tuple)
# ('Monitor', 299.99, 5)

# Create a copy with selected fields modified
discounted_item = replace(item, unit_price=249.99)
print(discounted_item)
```

---

## Dataclass Configuration Options

| Parameter | Default | Effect |
| :--- | :--- | :--- |
| `init` | `True` | Generates `__init__()` |
| `repr` | `True` | Generates `__repr__()` |
| `eq` | `True` | Generates `__eq__()` for structural comparison |
| `order` | `False` | Generates `__lt__()`, `__le__()`, `__gt__()`, `__ge__()` |
| `unsafe_hash` | `False` | Forces `__hash__()` generation even if mutable |
| `frozen` | `False` | Emulates immutability; raises error on attribute assignment |
| `match_args` | `True` | Generates `__match_args__` for structural pattern matching (3.10+) |
| `kw_only` | `False` | Forces all parameters to be keyword-only in `__init__` (3.10+) |
| `slots` | `False` | Uses `__slots__` for lower memory usage (3.10+) |
