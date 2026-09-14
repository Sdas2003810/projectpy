# Special Attributes Reference

Python objects, classes, functions, modules, and code frames expose special built-in attributes (often surrounded by double underscores) that reveal runtime metadata and execution state.

---

## Object & Class Attributes

| Attribute | Meaning | Example |
| :--- | :--- | :--- |
| `object.__class__` | The class to which an object belongs | `(42).__class__` -> `int` |
| `class.__name__` | The short name of the class | `User.__name__` -> `"User"` |
| `class.__qualname__` | Qualified dotted name showing class/function nesting | `"Outer.Inner"` (PEP 3155) |
| `class.__bases__` | Tuple of immediate base classes | `Dog.__bases__` -> `(Animal,)` |
| `class.__mro__` | Method Resolution Order tuple | `Dog.__mro__` |
| `class.__subclasses__()` | Method returning all direct child subclasses | `BasePlugin.__subclasses__()` |
| `object.__dict__` | Dictionary storing writable instance attributes | `u.__dict__` |
| `class.__slots__` | Restricts allowed instance attributes; removes `__dict__` | `__slots__ = ("x", "y")` |
| `object.__doc__` | The docstring of the object or class | `print(str.upper.__doc__)` |
| `object.__module__` | Name of the module where the object was defined | `"app.models"` |

---

## Function & Method Attributes

Functions are first-class objects with rich introspection capabilities:

```python
def example(a: int, b: str = "default", *, flag: bool = False) -> str:
    """A sample function demonstration."""
    return f"{a}-{b}-{flag}"
```

| Attribute | Value / Type | Purpose |
| :--- | :--- | :--- |
| `__name__` | `"example"` | Function name |
| `__qualname__` | `"example"` | Fully qualified name |
| `__doc__` | `"A sample function demonstration."` | Docstring |
| `__module__` | `"__main__"` | Defining module |
| `__defaults__` | `("default",)` | Tuple of positional default argument values |
| `__kwdefaults__`| `{"flag": False}` | Dict of keyword-only default values |
| `__annotations__`| `{'a': <class 'int'>, 'b': <class 'str'>, ...}` | Type hints mapping |
| `__code__` | `<code object example at ...>` | Compiled bytecode object |
| `__closure__` | `tuple` of cell objects (or `None`) | Variables captured by closure |
| `__wrapped__` | Underlying callable | Set automatically by `@functools.wraps` |

---

## Code Object Attributes (`function.__code__`)

The bytecode compiler produces a code object containing low-level instruction metadata:

```python
co = example.__code__

print("Argument count:      ", co.co_argcount)        # 2 (positional: a, b)
print("Keyword-only args:   ", co.co_kwonlyargcount)  # 1 (flag)
print("Local variable names:", co.co_varnames)        # ('a', 'b', 'flag')
print("Constants:           ", co.co_consts)          # (None, 'default', '-')
print("Source file:         ", co.co_filename)        # "<stdin>" or "script.py"
print("First line number:   ", co.co_firstlineno)     # Line where `def` starts
```

---

## Module Attributes

When a Python file executes as a module, Python populates these attributes in its global namespace:

```python
import sys

print(__name__)     # "__main__" when run directly, or "my_package.module" when imported
print(__file__)     # Absolute filesystem path to the source file
print(__doc__)      # Module-level docstring
print(__package__)  # Parent package name (e.g. "my_package")
print(__spec__)     # ModuleSpec object containing import metadata
```

- **`__all__`**: A list of string names defining the public API exported when a user writes `from module import *`.
