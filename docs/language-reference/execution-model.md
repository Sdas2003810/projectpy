# Execution Model

Python's execution model defines how code runs — how names are resolved, what a "scope" is, and why a variable defined inside a function isn't visible outside it.

---

## Code Blocks

Python executes code in units called **code blocks**. A new code block is created by:

- A module (the entire file is one block)
- A function body (each function has its own block)
- A class body
- A comprehension (`[... for ...]`)
- A generator expression
- `exec()` and `eval()` calls

Each code block has its own **namespace** — a dictionary mapping names to objects.

---

## Namespaces

A namespace is a mapping from names to objects. Python has several:

- **Built-in namespace**: `print`, `len`, `int`, `Exception`, etc. — always available
- **Global namespace**: The module-level namespace — one per module
- **Local namespace**: Created fresh each time a function is called

```python
# Global namespace
x = 10  # 'x' is in the module's global namespace

def my_func():
    y = 20  # 'y' is in my_func's local namespace
    print(x)  # 'x' found in global namespace
    print(y)  # 'y' found in local namespace
```

---

## The LEGB Scope Rule

When Python looks up a name, it searches in this order:

**L** → Local: current function's namespace  
**E** → Enclosing: any enclosing functions (closures)  
**G** → Global: the module-level namespace  
**B** → Built-in: Python's built-in namespace  

```python
x = "global"   # G

def outer():
    x = "enclosing"  # E

    def inner():
        x = "local"   # L
        print(x)      # L → "local"

    inner()
    print(x)          # E → "enclosing"

outer()
print(x)              # G → "global"
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">local
enclosing
global</div>

---

## The `global` Statement

By default, assignment inside a function creates a *new local* variable, even if a global with the same name exists. Use `global` to modify a global:

```python
count = 0

def increment():
    global count   # 'count' refers to the global, not a new local
    count += 1

increment()
increment()
print(count)  # 2
```

!!! tip "Avoid `global` when you can"
    Overusing `global` makes code hard to follow. Prefer returning values or using classes to encapsulate state.

---

## The `nonlocal` Statement

`nonlocal` lets an inner function modify a variable from an *enclosing* (but not global) function:

```python
def make_counter():
    count = 0   # enclosing variable

    def increment():
        nonlocal count   # refers to the enclosing 'count'
        count += 1
        return count

    return increment

counter = make_counter()
print(counter())  # 1
print(counter())  # 2
print(counter())  # 3
```

This is the correct way to write closures that modify state.

---

## Name Resolution in Class Bodies

Class bodies have their own scope during execution, but **methods don't see the class scope via normal LEGB lookup**:

```python
class Foo:
    x = 10

    def method(self):
        # print(x)    # NameError! 'x' is not in local or global scope
        print(self.x) # Correct: access via self
        print(Foo.x)  # Also correct: access via class name
```

This surprises many people coming from other languages.

---

## Builtins Can Be Shadowed

Because B (Built-in) is last in LEGB, any local or global with the same name shadows the built-in:

```python
# Don't do this:
list = [1, 2, 3]       # shadows the built-in 'list' type
print(list([4, 5]))    # TypeError: 'list' object is not callable

# Recovery:
del list               # removes the shadowing name
print(list([4, 5]))    # works again
```

Common accidental shadows: `list`, `dict`, `set`, `type`, `id`, `input`, `print`, `max`, `min`, `sum`, `len`.

---

## How Names are Resolved in Comprehensions

Comprehensions (list, dict, set, generator) have their own scope since Python 3. The iteration variable doesn't leak:

```python
x = 10
squares = [x**2 for x in range(5)]  # 'x' in comprehension is local to it
print(x)    # 10 — unchanged! (in Python 2 this would be 4)
```

This is a common gotcha for people coming from Python 2.

---

## Mutable Default Arguments and Closures

The LEGB rule interacts with default argument evaluation. Default argument values are evaluated once at function *definition* time, not at call time:

```python
import time

def log(message, timestamp=time.time()):  # evaluated ONCE when defined
    print(f"[{timestamp}] {message}")

# timestamp is the same every call
log("event 1")  # [1694700000.0] event 1 (example)
log("event 2")  # [1694700000.0] event 2 (same timestamp!)
```

For mutable defaults the problem is different — the object is shared across all calls. See the [Functions page](../fundamentals/functions.md) for the fix.

---

## Frame Objects

Each function call creates a **frame object** — a runtime record containing:

- The function's local namespace
- A reference to the previous frame (call stack)
- The code object being executed
- The current instruction pointer

You can inspect the current frame:

```python
import sys

def show_frame():
    frame = sys._getframe()
    print(f"Function: {frame.f_code.co_name}")
    print(f"File:     {frame.f_code.co_filename}")
    print(f"Line:     {frame.f_lineno}")
    print(f"Locals:   {frame.f_locals}")

show_frame()
```

This is used by debuggers, profilers, and tracing tools.
