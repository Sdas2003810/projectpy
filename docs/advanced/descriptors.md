# Python Descriptors

Descriptors are the engine powering Python's object-oriented machinery. Every time you use `@property`, define a regular method, call `@classmethod` or `@staticmethod`, or define slots, you are relying on descriptors under the hood.

A descriptor is any object that customizes what happens when an attribute is accessed, set, or deleted on an instance by implementing one or more of the descriptor protocol methods.

---

## The Descriptor Protocol

The descriptor protocol consists of three primary methods, plus one initialization hook:

```python
descr.__get__(self, obj, type=None) -> value
descr.__set__(self, obj, value) -> None
descr.__delete__(self, obj) -> None
descr.__set_name__(self, owner, name) -> None  # Added in Python 3.6
```

| Method | Invoked when |
| :--- | :--- |
| `__get__(self, instance, owner=None)` | Reading the attribute (`obj.attr` or `Owner.attr`) |
| `__set__(self, instance, value)` | Assigning the attribute (`obj.attr = value`) |
| `__delete__(self, instance)` | Deleting the attribute (`del obj.attr`) |
| `__set_name__(self, owner, name)` | Automatically called when the containing class is defined |

---

## Data Descriptors vs Non-Data Descriptors

Python classifies descriptors into two distinct categories, which determines their priority during attribute lookups:

1. **Data Descriptors**: Define `__set__()` and/or `__delete__()` (usually alongside `__get__()`).
2. **Non-Data Descriptors**: Define only `__get__()`.

```
                Attribute Access: `obj.attr`
                             |
                             v
           Does class have a DATA descriptor?
                       /           \
                     Yes            No
                     /                \
        Call descriptor.__get__()   Check `obj.__dict__["attr"]`
                                            /           \
                                         Found       Not Found
                                          /               \
                                    Return value   Does class have a
                                                   NON-DATA descriptor?
                                                       /         \
                                                     Yes          No
                                                     /              \
                                          Call __get__()   Check class dict
                                                                    |
                                                            Call __getattr__()
```

!!! important "Precedence Rule"
    **Data descriptors override instance dictionaries!** If `attr` is a data descriptor, `obj.attr = 10` invokes the descriptor's `__set__` method; it does **not** write to `obj.__dict__["attr"]`.
    In contrast, **non-data descriptors can be shadowed** by an entry in `obj.__dict__`.

---

## `__set_name__`: Automatic Attribute Binding

Prior to Python 3.6, descriptors required passing the attribute name to their constructor: `age = IntegerField("age")`.
With `__set_name__` (PEP 487), Python automatically informs the descriptor of its assigned variable name when the owner class is constructed:

```python
class ValidatedString:
    def __init__(self, min_length: int = 0):
        self.min_length = min_length
        self.attr_name = ""

    def __set_name__(self, owner, name):
        # Automatically called during class definition
        self.attr_name = f"_{name}"

    def __get__(self, instance, owner=None):
        if instance is None:
            # Accessed from class: Person.name
            return self
        return getattr(instance, self.attr_name, "")

    def __set__(self, instance, value):
        if not isinstance(value, str):
            raise TypeError(f"Expected str, got {type(value).__name__}")
        if len(value) < self.min_length:
            raise ValueError(f"String must be at least {self.min_length} characters long")
        setattr(instance, self.attr_name, value)
```

Using the descriptor:

```python
class Person:
    first_name = ValidatedString(min_length=2)
    last_name = ValidatedString(min_length=2)

    def __init__(self, first_name: str, last_name: str):
        self.first_name = first_name
        self.last_name = last_name

p = Person("Grace", "Hopper")
print(p.first_name)  # Grace

try:
    p.first_name = "A"  # Raises ValueError
except ValueError as e:
    print(f"Validation error: {e}")
```

<div class="terminal-output">
Grace
Validation error: String must be at least 2 characters long
</div>

---

## How Python Uses Descriptors Internally

### 1. Methods are Non-Data Descriptors

In Python, standard functions define a `__get__()` method:

```python
def greet(self):
    return f"Hello, {self.name}"

print(hasattr(greet, "__get__"))  # True
```

When you write `obj.greet()`, Python does:

```python
# Behind the scenes:
bound_method = greet.__get__(obj, type(obj))
bound_method()
```

The function's `__get__` creates a `types.MethodType` object that binds the function to the instance (`self`).

### 2. `@property` is a Data Descriptor

Python's built-in `property` is a data descriptor implemented in C. Here is how you can write a pure-Python equivalent:

```python
class CustomProperty:
    def __init__(self, fget=None, fset=None, fdel=None, doc=None):
        self.fget = fget
        self.fset = fset
        self.fdel = fdel
        self.__doc__ = doc or (fget.__doc__ if fget else None)

    def __get__(self, instance, owner=None):
        if instance is None:
            return self
        if self.fget is None:
            raise AttributeError("Unreadable attribute")
        return self.fget(instance)

    def __set__(self, instance, value):
        if self.fset is None:
            raise AttributeError("Can't set attribute")
        self.fset(instance, value)

    def __delete__(self, instance):
        if self.fdel is None:
            raise AttributeError("Can't delete attribute")
        self.fdel(instance)

    def setter(self, fset):
        return type(self)(self.fget, fset, self.fdel, self.__doc__)
```

---

## Practical Example: A Cached Property Descriptor

Before `functools.cached_property` was introduced in Python 3.8, developers wrote caching descriptors to compute expensive properties on demand and store the result directly in the instance dictionary:

```python
import time

class LazyProperty:
    """Non-data descriptor that caches the result in the instance dict."""
    def __init__(self, function):
        self.function = function
        self.name = function.__name__

    def __get__(self, instance, owner=None):
        if instance is None:
            return self
        
        # Compute expensive value
        val = self.function(instance)
        # Store in instance dict: shadows this non-data descriptor on next access!
        instance.__dict__[self.name] = val
        return val

class DataReport:
    @LazyProperty
    def heavy_dataset(self):
        print("Fetching and calculating dataset (expensive)...")
        time.sleep(0.5)
        return [x ** 2 for x in range(5)]

report = DataReport()
print(report.heavy_dataset)  # Calls calculation
print(report.heavy_dataset)  # Instant: served from report.__dict__
```

<div class="terminal-output">
Fetching and calculating dataset (expensive)...
[0, 1, 4, 9, 16]
[0, 1, 4, 9, 16]
</div>

---

## Key Rules for Writing Descriptors

1. **Store state on the instance, not the descriptor**: Descriptors are class-level attributes shared across all instances. If you store state on `self.value` inside the descriptor, every instance will overwrite the same value! Always store on `instance` or in a dictionary keyed by `id(instance)`.
2. **Handle `instance is None`**: When accessed from the class (e.g., `Person.first_name`), `instance` is `None`. Always return the descriptor object itself (`return self`).
3. **Prefer `__set_name__`**: Avoid hardcoding attribute names or requiring duplicate strings in the constructor.
