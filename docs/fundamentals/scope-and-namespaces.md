# Scope and Namespaces

Understanding scope is one of those things that separates programmers who "know Python" from programmers who *understand* Python. It explains why a variable defined in a function isn't visible outside it, and why modifying a global inside a function requires the `global` keyword.

---

## What Is a Namespace?

A namespace is a dictionary mapping names to objects. When you write `x = 42`, Python stores the name `"x"` in the current namespace, pointing at the integer object `42`.

Python maintains several namespaces at any point:

- **Built-in**: `print`, `len`, `int`, `range`, `Exception`, etc. — always available
- **Global**: The module-level namespace — one per `.py` file
- **Local**: Created fresh for each function call — destroyed when the function returns
- **Enclosing**: Any intermediate function scopes (for closures)

```python
# Global namespace
x = 10          # 'x' lives in the module's global namespace

def outer():
    y = 20      # 'y' lives in outer()'s local namespace

    def inner():
        z = 30  # 'z' lives in inner()'s local namespace
```

---

## The LEGB Rule

When Python looks up a name, it searches scopes in this exact order:

1. **L**ocal — the current function
2. **E**nclosing — any enclosing functions (inner to outer)
3. **G**lobal — the module level
4. **B**uilt-in — Python's built-in names

The first match wins. If not found in any scope, you get a `NameError`.

```python
x = "global"            # G — global scope

def outer():
    x = "enclosing"     # E — enclosing scope

    def inner():
        x = "local"     # L — local scope
        print(x)        # finds 'x' in L → "local"

    inner()
    print(x)            # finds 'x' in E → "enclosing"

outer()
print(x)                # finds 'x' in G → "global"
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">local
enclosing
global</div>

### Built-in Lookup

```python
# 'len' is in the built-in scope — always accessible
print(len([1, 2, 3]))   # 3

# You can shadow built-ins (but don't!)
len = 100               # shadows the built-in len
print(len([1, 2, 3]))   # TypeError: 'int' object is not callable

del len                 # restore access to the built-in
print(len([1, 2, 3]))   # 3
```

---

## Local Scope — Functions

Every function call creates a fresh local namespace. Variables defined inside a function are local to it:

```python
def calculate():
    result = 42     # local to calculate()
    return result

calculate()
# print(result)   # NameError! 'result' doesn't exist here
```

Parameters are also local:

```python
def greet(name):    # 'name' is local
    message = f"Hello, {name}"
    return message

greet("Alice")
# print(name)     # NameError
```

---

## Global Scope

Code at the top level of a module lives in the global namespace. Functions can **read** global variables without any special keyword:

```python
threshold = 100     # global

def is_over_threshold(value):
    return value > threshold   # reads the global 'threshold' — fine
```

But **assigning** to a name inside a function creates a **new local** — it does NOT modify the global:

```python
count = 0

def increment():
    count = count + 1  # UnboundLocalError! Python sees 'count =' and
                       # decides 'count' is local — but then tries to read
                       # it before it's assigned

increment()  # UnboundLocalError: local variable 'count' referenced before assignment
```

This surprises many people. Python decides at compile time whether a name is local (if it appears on the left side of `=` anywhere in the function), so the read `count + 1` tries to read the local `count` which hasn't been assigned yet.

---

## The `global` Statement

Use `global` to tell Python that a name refers to the global scope:

```python
count = 0

def increment():
    global count        # 'count' now refers to the module-level 'count'
    count = count + 1

increment()
increment()
increment()
print(count)    # 3
```

!!! tip "When to use `global`"
    Use it sparingly. Mutable global state makes code hard to test and reason about. Prefer returning values or using classes to encapsulate state.

---

## Enclosing Scope and Closures

When a function is defined inside another function, it has access to the enclosing function's local variables — even after the outer function has returned. This is called a **closure**:

```python
def make_multiplier(factor):
    # 'factor' is in the enclosing scope of the inner function
    def multiply(x):
        return x * factor   # 'factor' captured from enclosing scope
    return multiply

double = make_multiplier(2)
triple = make_multiplier(3)

print(double(5))   # 10
print(triple(5))   # 15
# 'factor' lives on because it's referenced by the closure
```

---

## The `nonlocal` Statement

A closure can read enclosing variables freely, but assigning to them creates a new local (same issue as `global`). Use `nonlocal` to modify an enclosing variable:

```python
def make_counter():
    count = 0

    def increment():
        nonlocal count      # 'count' refers to the enclosing 'count'
        count += 1
        return count

    return increment

counter = make_counter()
print(counter())   # 1
print(counter())   # 2
print(counter())   # 3
```

`nonlocal` searches through enclosing scopes (not global, not built-in) for the name. It's an error if the name isn't found in any enclosing scope.

---

## Class Scope — A Special Case

Class bodies have their own namespace during execution, but **methods don't see the class scope** via normal LEGB lookup:

```python
class Config:
    DEBUG = True
    MAX_RETRIES = 3

    def is_debug(self):
        # return DEBUG        # NameError! Not in local or global scope
        return Config.DEBUG   # Correct — access via class name
        # return self.DEBUG   # Also correct — via instance

Config().is_debug()   # True
```

This surprises everyone from Java/C++ backgrounds where `DEBUG` would be visible inside a method.

---

## Comprehension Scope

List, dict, and set comprehensions have their own scope in Python 3. The iteration variable doesn't leak:

```python
x = "outer"
result = [x for x in range(5)]   # 'x' inside comprehension is local to it
print(x)        # "outer" — unchanged! (In Python 2 this would be 4)
print(result)   # [0, 1, 2, 3, 4]
```

Generator expressions also have their own scope.

---

## `locals()` and `globals()`

These built-in functions return the current local and global namespaces as dictionaries:

```python
x = 10
y = 20

def show_namespaces():
    a = 1
    b = 2
    print("Locals:", locals())    # {'a': 1, 'b': 2}
    print("Globals:", list(globals().keys())[:5])  # first few global names

show_namespaces()
```

!!! warning "`locals()` returns a copy"
    Modifying the dict returned by `locals()` does **not** change the actual local variables (in most cases). `globals()` returns a live reference — modifying it does affect globals, but avoid doing so in production code.

---

## `vars()` and `dir()`

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

p = Point(1, 2)
print(vars(p))          # {'x': 1, 'y': 2} — instance's __dict__
print(dir(p))           # all attributes including inherited ones
print(vars(Point))      # class's namespace (a mappingproxy)
```
