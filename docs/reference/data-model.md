# Data Model Reference Index

A comprehensive quick-reference catalog of Python's standard type hierarchy and special dunder method signatures.

---

## Standard Type Hierarchy

Every entity in Python is an object. Each object possesses an **identity** (memory address, returned by `id()`), a **type** (returned by `type()`), and a **value**.

```
Object
 ├── None (type: NoneType)
 ├── NotImplemented (type: NotImplementedType)
 ├── Ellipsis (...) (type: ellipsis)
 ├── Numbers
 │    ├── Integral (int, bool)
 │    ├── Real (float)
 │    └── Complex (complex)
 ├── Sequences
 │    ├── Immutable (str, tuple, bytes)
 │    └── Mutable (list, bytearray)
 ├── Set Types
 │    ├── frozenset (Immutable)
 │    └── set (Mutable)
 ├── Mappings
 │    └── dict
 ├── Callable Types
 │    ├── User-defined functions
 │    ├── Built-in functions and methods
 │    ├── Generator functions
 │    ├── Coroutine functions
 │    └── Classes (calling invokes __new__ and __init__)
 └── Internal Types
      ├── Code objects
      ├── Frame objects
      ├── Traceback objects
      └── Slice objects
```

---

## Complete Dunder Method Catalog

### 1. Object Lifecycle & Representation

| Method | Invocation | Purpose |
| :--- | :--- | :--- |
| `__new__(cls, ...)` | `cls(...)` | Allocates new instance memory |
| `__init__(self, ...)` | `cls(...)` | Initializes instance attributes |
| `__del__(self)` | Garbage collection | Teardown before deallocation |
| `__repr__(self)` | `repr(obj)` | Unambiguous official string representation |
| `__str__(self)` | `str(obj)`, `print(obj)` | User-friendly readable string |
| `__format__(self, spec)`| `f"{obj:spec}"` | Custom formatting string spec |
| `__hash__(self)` | `hash(obj)` | Integer hash for set/dict membership |
| `__bool__(self)` | `bool(obj)`, `if obj:` | Truth value testing |

### 2. Comparison Operations

| Method | Operator | Meaning |
| :--- | :--- | :--- |
| `__lt__(self, other)` | `<` | Less than |
| `__le__(self, other)` | `<=` | Less than or equal |
| `__eq__(self, other)` | `==` | Equality |
| `__ne__(self, other)` | `!=` | Inequality |
| `__gt__(self, other)` | `>` | Greater than |
| `__ge__(self, other)` | `>=` | Greater than or equal |

### 3. Attribute Access

| Method | Invocation | Purpose |
| :--- | :--- | :--- |
| `__getattr__(self, name)` | `obj.name` | Fallback when attribute is NOT found |
| `__getattribute__(self, name)`| `obj.name` | Unconditional attribute lookup |
| `__setattr__(self, name, val)`| `obj.name = val` | Assigning instance attribute |
| `__delattr__(self, name)` | `del obj.name` | Deleting instance attribute |
| `__dir__(self)` | `dir(obj)` | List accessible attribute names |

### 4. Container & Sequence Protocol

| Method | Invocation | Purpose |
| :--- | :--- | :--- |
| `__len__(self)` | `len(obj)` | Element count |
| `__getitem__(self, key)` | `obj[key]` | Read item by key or slice |
| `__setitem__(self, key, val)`| `obj[key] = val` | Write item by key or slice |
| `__delitem__(self, key)` | `del obj[key]` | Delete item by key or slice |
| `__iter__(self)` | `iter(obj)`, `for x in obj:` | Return an iterator |
| `__next__(self)` | `next(iter)` | Advance iterator to next item |
| `__contains__(self, item)`| `item in obj` | Membership test |
| `__reversed__(self)` | `reversed(obj)` | Reverse iteration |

### 5. Arithmetic & Bitwise Operations

| Operation | Infix | Reflected (Right) | In-Place |
| :--- | :--- | :--- | :--- |
| **Addition** | `__add__(self, other)` | `__radd__` | `__iadd__` |
| **Subtraction** | `__sub__(self, other)` | `__rsub__` | `__isub__` |
| **Multiplication**| `__mul__(self, other)` | `__rmul__` | `__imul__` |
| **Matrix Mul** | `__matmul__(self, other)` | `__rmatmul__` | `__imatmul__` |
| **True Division** | `__truediv__(self, other)`| `__rtruediv__` | `__itruediv__` |
| **Floor Division**| `__floordiv__(self, other)`| `__rfloordiv__` | `__ifloordiv__` |
| **Modulo** | `__mod__(self, other)` | `__rmod__` | `__imod__` |
| **Power** | `__pow__(self, other)` | `__rpow__` | `__ipow__` |
| **Bitwise AND** | `__and__(self, other)` | `__rand__` | `__iand__` |
| **Bitwise OR** | `__or__(self, other)` | `__ror__` | `__ior__` |
| **Bitwise XOR** | `__xor__(self, other)` | `__rxor__` | `__ixor__` |
| **Left Shift** | `__lshift__(self, other)` | `__rlshift__` | `__ilshift__` |
| **Right Shift** | `__rshift__(self, other)` | `__rrshift__` | `__irshift__` |

### 6. Context Managers

| Method | Invocation | Purpose |
| :--- | :--- | :--- |
| `__enter__(self)` | `with obj:` | Enter context block |
| `__exit__(self, exc_type, exc_val, tb)` | `with obj:` | Exit context block & clean up |
| `__aenter__(self)` | `async with obj:` | Async enter context block |
| `__aexit__(self, exc_type, exc_val, tb)` | `async with obj:` | Async exit context block |
