# Modules, Packages, and Virtual Environments

As your program grows, splitting code into reusable files and packages becomes necessary. Python uses **modules** and **packages** for code organization, and **virtual environments** for isolated dependency management.

---

## 1. Modules

A **module** is simply a Python file containing statements and definitions ending in `.py`.

Suppose you create a file named `math_utils.py`:

```python
# math_utils.py
def square(x: float) -> float:
    return x * x

PI = 3.14159
```

You can now import it in another script:

```python
# main.py
import math_utils

print(math_utils.square(4)) # 16.0
print(math_utils.PI)        # 3.14159
```

### Import Styles

```python
# Direct import
import math
print(math.sqrt(25))

# Specific attribute import
from math import sqrt, ceil
print(sqrt(25))

# Import with alias
import datetime as dt
print(dt.date.today())
```

---

## Module Search Path (`sys.path`)

When a module named `example` is imported, the interpreter searches for it in this sequence:
1. Built-in modules compiled into the interpreter.
2. The directory containing the input script (or the current directory).
3. `PYTHONPATH` (a list of directory names).
4. Installation-dependent default directory (site-packages).

These paths are stored in the list `sys.path`:

```python
import sys
print(sys.path)
```

---

## 2. Packages (`__init__.py`)

A **package** is a directory that contains multiple modules. It typically contains an `__init__.py` file:

```text
my_project/
├── my_package/
│   ├── __init__.py
│   ├── database.py
│   └── helpers.py
└── main.py
```

### Exposing Package Contents
Inside `my_package/__init__.py`:

```python
from .database import connect
from .helpers import format_data

__all__ = ["connect", "format_data"]
```

Users can import from the package directly:
```python
from my_package import connect, format_data
```

---

## 3. Virtual Environments (`venv`)

A **virtual environment** is a self-contained directory tree that contains a specific Python installation plus several additional packages. It prevents dependency conflicts between different projects.

### Setting Up a Virtual Environment

=== "Windows"
    ```powershell
    # 1. Create the environment
    python -m venv .venv

    # 2. Activate the environment
    .venv\Scripts\activate

    # 3. Install required packages
    pip install requests

    # 4. Deactivate when finished
    deactivate
    ```

=== "macOS / Linux"
    ```bash
    # 1. Create the environment
    python3 -m venv .venv

    # 2. Activate the environment
    source .venv/bin/activate

    # 3. Install required packages
    pip install requests

    # 4. Deactivate when finished
    deactivate
    ```

---

## Managing Dependencies with `requirements.txt`

```bash
# Save installed packages and their exact versions to a file:
pip freeze > requirements.txt

# Install all packages listed in a requirements file:
pip install -r requirements.txt
```
