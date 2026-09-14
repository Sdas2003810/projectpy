# The Data Model

Python's data model is the set of rules that define how objects behave. When you write `a + b`, Python calls `a.__add__(b)`. When you use `len(x)`, Python calls `x.__len__()`. When you do `for item in x`, Python calls `iter(x)` which calls `x.__iter__()`.

Understanding the data model means you can make your own classes behave like built-in types. This is one of Python's most powerful design decisions.

---

## Objects, Values, and Types

Every piece of data in Python is an **object**. Every object has:

- **Identity**: A unique identifier — `id(obj)` returns it. In CPython, it's the memory address. Never changes during an object's lifetime.
- **Type**: What kind of object it is — `type(obj)` returns it. Determines what operations are supported. Never changes for an object.
- **Value**: The actual data. May change (mutable) or not (immutable).

```python
x = [1, 2, 3]
print(id(x))    # e.g. 140543763210560
print(type(x))  # <class 'list'>
x.append(4)     # value changed (mutable)
print(id(x))    # SAME id — still the same object
```

---

## The `is` vs `==` Distinction

- `is` compares **identity** — are these the same object?
- `==` compares **value** — do these objects have equal content?

```python
a = [1, 2, 3]
b = [1, 2, 3]
c = a

print(a == b)   # True  — same value
print(a is b)   # False — different objects
print(a is c)   # True  — same object (c is just another name for a)
```

!!! warning "Don't use `is` for value comparison"
    `is` is only for identity. Use `==` for value equality. The one exception: comparing to singletons like `None`, `True`, `False` — conventionally written as `x is None`, not `x == None`.

---

## Special Methods (Dunder Methods)

Python uses **double-underscore methods** (dunders) to define how objects respond to operators and built-in functions. This is often called "operator overloading".

### Numeric Operations

| Method | Triggered by | Notes |
|--------|-------------|-------|
| `__add__(self, other)` | `self + other` | |
| `__sub__(self, other)` | `self - other` | |
| `__mul__(self, other)` | `self * other` | |
| `__truediv__(self, other)` | `self / other` | |
| `__floordiv__(self, other)` | `self // other` | |
| `__mod__(self, other)` | `self % other` | |
| `__pow__(self, other)` | `self ** other` | |
| `__neg__(self)` | `-self` | Unary |
| `__abs__(self)` | `abs(self)` | |

Reflected versions: `__radd__`, `__rsub__`, etc. — called when the left operand doesn't support the operation.

### Comparison

| Method | Triggered by |
|--------|-------------|
| `__eq__(self, other)` | `self == other` |
| `__ne__(self, other)` | `self != other` |
| `__lt__(self, other)` | `self < other` |
| `__le__(self, other)` | `self <= other` |
| `__gt__(self, other)` | `self > other` |
| `__ge__(self, other)` | `self >= other` |

### Container Protocol

| Method | Triggered by |
|--------|-------------|
| `__len__(self)` | `len(self)` |
| `__getitem__(self, key)` | `self[key]` |
| `__setitem__(self, key, value)` | `self[key] = value` |
| `__delitem__(self, key)` | `del self[key]` |
| `__contains__(self, item)` | `item in self` |
| `__iter__(self)` | `iter(self)`, `for x in self` |
| `__next__(self)` | `next(self)` |

### Object Lifecycle

| Method | Called when |
|--------|------------|
| `__new__(cls, ...)` | Creating a new instance |
| `__init__(self, ...)` | Initializing a new instance |
| `__del__(self)` | Object is about to be garbage collected |

### String Representation

| Method | Called by |
|--------|----------|
| `__repr__(self)` | `repr(obj)`, REPL display |
| `__str__(self)` | `str(obj)`, `print(obj)` |
| `__format__(self, spec)` | `format(obj, spec)`, f-strings |
| `__bytes__(self)` | `bytes(obj)` |

### Attribute Access

| Method | Called when |
|--------|------------|
| `__getattribute__(self, name)` | Any attribute access `obj.attr` |
| `__getattr__(self, name)` | Attribute not found via normal means |
| `__setattr__(self, name, value)` | `obj.attr = value` |
| `__delattr__(self, name)` | `del obj.attr` |
| `__dir__(self)` | `dir(obj)` |

### Context Manager Protocol

| Method | Called by |
|--------|----------|
| `__enter__(self)` | Entering `with` block |
| `__exit__(self, exc_type, exc_val, exc_tb)` | Leaving `with` block |

---

## Example: A Complete Custom Class

Here's a `Vector` class that uses the data model to behave like a proper numeric type:

```python
import math

class Vector:
    """2D vector with full operator support."""

    def __init__(self, x: float, y: float):
        self.x = x
        self.y = y

    def __repr__(self) -> str:
        return f"Vector({self.x!r}, {self.y!r})"

    def __str__(self) -> str:
        return f"({self.x}, {self.y})"

    def __add__(self, other: "Vector") -> "Vector":
        return Vector(self.x + other.x, self.y + other.y)

    def __sub__(self, other: "Vector") -> "Vector":
        return Vector(self.x - other.x, self.y - other.y)

    def __mul__(self, scalar: float) -> "Vector":
        return Vector(self.x * scalar, self.y * scalar)

    def __rmul__(self, scalar: float) -> "Vector":
        return self.__mul__(scalar)

    def __abs__(self) -> float:
        return math.sqrt(self.x**2 + self.y**2)

    def __bool__(self) -> bool:
        return self.x != 0 or self.y != 0

    def __eq__(self, other: object) -> bool:
        if not isinstance(other, Vector):
            return NotImplemented
        return self.x == other.x and self.y == other.y

    def __iter__(self):
        yield self.x
        yield self.y

    def __len__(self) -> int:
        return 2

    def __getitem__(self, index: int) -> float:
        return (self.x, self.y)[index]


v1 = Vector(1, 2)
v2 = Vector(3, 4)

print(v1 + v2)      # (4, 6)
print(3 * v1)       # (3, 6)
print(abs(v2))      # 5.0
print(bool(v1))     # True
print(list(v1))     # [1, 2]
print(v1[0])        # 1
```

---

## Type Hierarchy

Python's built-in types form a hierarchy rooted at `object`:

```
object
├── NoneType        (None)
├── bool            (True, False)
├── int
│   └── bool        (bool is a subclass of int!)
├── float
├── complex
├── str
├── bytes
├── bytearray
├── memoryview
├── list
├── tuple
├── range
├── set
├── frozenset
├── dict
├── type            (metaclass — classes are instances of type)
├── function
├── method
├── module
└── ...
```

```python
print(isinstance(True, int))   # True — bool is a subclass of int!
print(isinstance(True, bool))  # True
print(True + 1)                # 2 — True is literally 1
```
