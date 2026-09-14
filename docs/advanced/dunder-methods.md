# Special (Dunder) Methods

In Python, special methods are identified by leading and trailing double underscores (such as `__init__` or `__str__`). These are formally known as **special methods**, and colloquially referred to as **dunder methods**.

Dunder methods enable operator overloading and define how user-defined classes interact with Python's built-in syntax.

---

## 1. Object Representation: `__str__` vs `__repr__`

- `__str__`: Called by `str(object)` and `print()`. Returns an informal, readable string representation intended for end-users.
- `__repr__`: Called by `repr(object)` and the interactive interpreter. Returns an unambiguous representation (ideally valid Python code to recreate the object) intended for developers and debugging.

<div class="example-box">
<div class="example-title">Example: Implementing __str__ and __repr__</div>

```python
class Point:
    def __init__(self, x: float, y: float):
        self.x = x
        self.y = y

    def __repr__(self) -> str:
        return f"Point({self.x}, {self.y})"

    def __str__(self) -> str:
        return f"({self.x}, {self.y})"

p = Point(3.5, 7.0)
print(str(p))   # (3.5, 7.0)  <- from __str__
print(repr(p))  # Point(3.5, 7.0) <- from __repr__
```
</div>

---

## 2. Operator Overloading

You can overload Python arithmetic and comparison operators by implementing the corresponding dunder method:

| Operator | Dunder Method | Operation |
| :--- | :--- | :--- |
| `+` | `__add__(self, other)` | Addition |
| `-` | `__sub__(self, other)` | Subtraction |
| `*` | `__mul__(self, other)` | Multiplication |
| `/` | `__truediv__(self, other)` | True division |
| `//` | `__floordiv__(self, other)`| Floor division |
| `==` | `__eq__(self, other)` | Equality |
| `<` | `__lt__(self, other)` | Less than |
| `<=` | `__le__(self, other)` | Less than or equal to |

<div class="example-box">
<div class="example-title">Example: Overloading the Addition Operator</div>

```python
class Vector:
    def __init__(self, x: int, y: int):
        self.x = x
        self.y = y

    def __add__(self, other: "Vector") -> "Vector":
        return Vector(self.x + other.x, self.y + other.y)

    def __repr__(self) -> str:
        return f"Vector({self.x}, {self.y})"

v1 = Vector(2, 4)
v2 = Vector(3, 1)
v3 = v1 + v2
print(v3)  # Vector(5, 5)
```
</div>

---

## 3. Emulating Containers: `__len__` and `__getitem__`

Implementing `__len__` and `__getitem__` allows an object to behave like a sequence:

```python
class CustomList:
    def __init__(self, items):
        self._items = list(items)

    def __len__(self) -> int:
        return len(self._items)

    def __getitem__(self, index):
        return self._items[index]

my_seq = CustomList(["apple", "banana", "cherry"])
print(len(my_seq))      # 3
print(my_seq[1])        # 'banana'
print("apple" in my_seq)# True
```

---

## 4. Callable Objects: `__call__`

Defining `__call__` allows an instance of a class to be called like a function:

```python
class Multiplier:
    def __init__(self, factor: int):
        self.factor = factor

    def __call__(self, value: int) -> int:
        return self.factor * value

double = Multiplier(2)
print(double(15))  # 30
```

---

## 5. Memory Optimization with `__slots__`

By default, class instances store attributes in a dictionary called `__dict__`. For classes instantiated millions of times, you can define `__slots__` to allocate fixed attribute memory, significantly decreasing RAM usage:

```python
class CompactPoint:
    __slots__ = ("x", "y")

    def __init__(self, x: float, y: float):
        self.x = x
        self.y = y
```
