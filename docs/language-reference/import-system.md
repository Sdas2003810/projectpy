# The Import System

Every time you write `import os` or `from pathlib import Path`, Python's import system kicks in. Understanding how it works helps you debug mysterious `ModuleNotFoundError`s, write packages correctly, and understand `sys.path`.

---

## How `import` Works — Overview

When you `import foo`, Python:

1. **Checks `sys.modules`** — a cache of already-imported modules. If `foo` is there, returns it immediately. No re-importing.
2. **Finds the module** using `sys.meta_path` finders — scans `sys.path` locations.
3. **Loads the module** — reads the source file, compiles to bytecode, caches in `__pycache__`.
4. **Executes the module** — runs the module body top-to-bottom.
5. **Caches in `sys.modules`** — so future imports of the same module are instant.
6. **Binds the name** — assigns the module to the name in the current namespace.

```python
import sys
import os

# os is now in sys.modules
print("os" in sys.modules)   # True
print(sys.modules["os"] is os)  # True — same object
```

---

## `sys.path` — Where Python Looks

Python searches for modules in the directories listed in `sys.path`:

```python
import sys
for path in sys.path:
    print(path)
```

Typical output:
```
(empty string — current directory)
/usr/lib/python314.zip
/usr/lib/python3.14
/usr/lib/python3.14/lib-dynload
/home/user/.local/lib/python3.14/site-packages
/usr/lib/python3/dist-packages
```

The empty string `""` means "the directory containing the script being run" (or the current working directory in interactive mode).

!!! warning "Modifying `sys.path`"
    You can append to `sys.path` at runtime, but this is generally a sign that you need a proper package structure instead. For normal projects, use `pip install -e .` or set `PYTHONPATH`.

---

## Package Structure

A **package** is a directory containing an `__init__.py` file. This signals to Python that the directory is a Python package, not just a folder.

```
mypackage/
├── __init__.py          ← makes this a package
├── core.py
├── utils.py
└── subpackage/
    ├── __init__.py      ← makes subpackage a package too
    └── helpers.py
```

```python
# Importing from this structure
import mypackage              # runs mypackage/__init__.py
import mypackage.core         # runs mypackage/core.py
from mypackage import utils   # imports utils module
from mypackage.subpackage import helpers
```

---

## Namespace Packages (PEP 420)

Since Python 3.3, you can have packages *without* `__init__.py`. These are **namespace packages**, useful when a package is spread across multiple directories (e.g., multiple separate distributions contributing to the same namespace).

For regular packages (single directory, your own code), always include `__init__.py`.

---

## `__init__.py` Contents

The `__init__.py` runs when the package is first imported. Use it to:

- Define the package's public API (`__all__`)
- Import commonly used symbols so users can do `from mypackage import MyClass`
- Set up package-level configuration

```python
# mypackage/__init__.py
from .core import MyClass, process  # relative import
from .utils import helper

__version__ = "1.0.0"
__all__ = ["MyClass", "process", "helper"]
```

---

## Relative vs Absolute Imports

Inside a package, you can use **relative imports** (starting with `.`):

```python
# Absolute import — works anywhere
from mypackage.core import MyClass

# Relative import — only works inside a package
from .core import MyClass      # sibling module
from ..utils import something  # parent package
from ...top import other       # grandparent (rare)
```

!!! tip "When to use relative imports"
    Use relative imports inside your package for intra-package references. Use absolute imports for external dependencies and in scripts. Relative imports make it easier to rename/move your package without updating every internal import.

---

## The `__pycache__` Directory

Python compiles `.py` source files to bytecode (`.pyc` files) and caches them in a `__pycache__` subdirectory:

```
mypackage/
├── __init__.py
├── core.py
└── __pycache__/
    ├── __init__.cpython-314.pyc
    └── core.cpython-314.pyc
```

The `.pyc` files include the Python version tag (`cpython-314` for Python 3.14) so multiple Python versions coexist without conflicts. Python automatically uses the cached `.pyc` if the source hasn't changed (checked via mtime and file size).

Add `__pycache__/` to your `.gitignore`.

---

## `importlib` — Import as a First-Class API

The `importlib` module gives you programmatic access to the import system:

```python
import importlib

# Import a module by string name (useful for plugins, dynamic loading)
module = importlib.import_module("json")
print(module.dumps({"key": "value"}))

# Check if a module exists without importing it
spec = importlib.util.find_spec("numpy")
if spec is None:
    print("numpy is not installed")
else:
    print(f"numpy found at: {spec.origin}")

# Reload a module (useful in development, REPL)
import mymodule
importlib.reload(mymodule)
```

---

## Circular Imports

Circular imports happen when module A imports module B, and module B imports module A. Python handles them (it won't loop forever), but you may get `ImportError` or `AttributeError` because part of the module isn't defined yet when the circular import runs.

```python
# a.py
from b import greet_b  # triggers b.py to load, which tries to import a.py

# b.py
from a import greet_a  # a.py is partially loaded — may fail
```

**Fix**: Move the circular import inside the function that needs it, or restructure so there's no cycle.

```python
# b.py — deferred import
def greet_b():
    from a import greet_a  # imported only when called, a.py is fully loaded by then
    greet_a()
```

---

## `__all__`

Defines what gets exported when someone does `from mypackage import *`:

```python
# mymodule.py
__all__ = ["public_function", "PublicClass"]

def public_function():
    pass

def _private_function():   # won't be exported via *
    pass

class PublicClass:
    pass
```

If `__all__` is not defined, `from module import *` imports all names not starting with `_`.
