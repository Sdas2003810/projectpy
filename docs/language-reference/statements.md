# Simple Statements

Simple statements are one-liner constructs — they fit on a single logical line. Multiple simple statements can appear on one physical line separated by semicolons (though PEP 8 discourages this).

---

## `assert`

Checks a condition at runtime. If the condition is falsy, raises `AssertionError`:

```python
def divide(a: float, b: float) -> float:
    assert b != 0, f"Divisor cannot be zero, got {b!r}"
    return a / b

divide(10, 2)    # OK
divide(10, 0)    # AssertionError: Divisor cannot be zero, got 0
```

!!! warning "Assertions are disabled with `-O` flag"
    `python -O yourscript.py` strips all `assert` statements. Never use `assert` for security or data validation in production — use explicit `if` checks and raise `ValueError` or similar.

---

## `pass`

Does nothing. Used as a placeholder where a statement is syntactically required:

```python
class TODO:
    pass   # Skeleton class — will implement later

def not_yet():
    pass   # Placeholder function

# Also valid in if/else
if some_condition:
    pass   # nothing to do here
else:
    handle()
```

---

## `del`

Removes a name binding or a container item. The object may or may not be garbage collected (depends on whether other references exist):

```python
x = [1, 2, 3, 4, 5]
del x[2]      # removes element at index 2
print(x)      # [1, 2, 4, 5]

del x[1:3]    # removes elements at index 1 and 2
print(x)      # [1, 5]

name = "Alice"
del name      # unbinds the name 'name'
# print(name)  # NameError
```

---

## `return`

Returns a value from a function. If omitted or empty, returns `None`:

```python
def add(a, b):
    return a + b    # returns the sum

def nothing():
    return          # returns None explicitly

def also_nothing():
    pass            # implicitly returns None
```

A function can return multiple values using a tuple (packing):

```python
def divmod_custom(a, b):
    return a // b, a % b   # returns a tuple

quotient, remainder = divmod_custom(17, 5)  # tuple unpacking
print(quotient, remainder)  # 3 2
```

---

## `yield` and `yield from`

Turns a function into a generator. See the [Iterators and Generators](../core/iterators-and-generators.md) page for full details:

```python
def count_up(n):
    for i in range(n):
        yield i    # pauses here and sends i to the caller

for num in count_up(5):
    print(num)  # 0, 1, 2, 3, 4

# yield from — delegates to another iterable/generator
def flatten(nested):
    for sublist in nested:
        yield from sublist

list(flatten([[1,2], [3,4], [5]]))  # [1, 2, 3, 4, 5]
```

---

## `raise`

Raises an exception:

```python
# Raise a new exception
raise ValueError("value must be positive")

# Raise with a cause (chained exceptions)
try:
    int("not_a_number")
except ValueError as e:
    raise RuntimeError("Configuration parse failed") from e

# Re-raise the current exception (inside except block)
try:
    risky_operation()
except Exception:
    log_error()
    raise  # re-raises the same exception with full traceback
```

---

## `break` and `continue`

Control loop flow:

```python
# break — exits the loop entirely
for i in range(10):
    if i == 5:
        break
    print(i)   # 0, 1, 2, 3, 4

# continue — skips the rest of the current iteration
for i in range(10):
    if i % 2 == 0:
        continue
    print(i)   # 1, 3, 5, 7, 9

# break/continue work in while too
while True:
    data = read_data()
    if not data:
        break
    process(data)
```

---

## `import`

Imports a module or specific names:

```python
import os                          # imports the 'os' module
import os.path                     # imports the submodule
import numpy as np                 # alias
from os import getcwd, listdir     # imports specific names
from os.path import join as pjoin  # alias for specific name
from . import utils                # relative import (inside a package)
from .utils import helper          # relative import of specific name
```

---

## `global` and `nonlocal`

Declare that a name refers to a non-local scope (see [Execution Model](execution-model.md)):

```python
x = 0

def increment():
    global x
    x += 1

def outer():
    count = 0

    def inner():
        nonlocal count
        count += 1

    inner()
    return count
```

---

## `type` — Type Alias Statement (Python 3.12+)

Creates a type alias without the `TypeAlias` annotation:

```python
# Python 3.12+
type Point = tuple[float, float]
type Matrix[T] = list[list[T]]   # generic
```

---

## Assignment Statements

```python
# Simple assignment
x = 42

# Multiple assignment
a = b = c = 0

# Tuple unpacking
x, y = 1, 2
x, y = y, x   # swap!

# Extended unpacking
first, *rest = [1, 2, 3, 4, 5]
*most, last = [1, 2, 3, 4, 5]
first, *middle, last = [1, 2, 3, 4, 5]

# Augmented assignment
x += 1
x -= 1
x *= 2
x /= 2
x //= 2
x **= 2
x %= 2
x &= 0b1111
x |= 0b0001
x ^= 0b1010
x <<= 1
x >>= 1
x @= matrix  # matrix multiply (for numpy etc.)
```
