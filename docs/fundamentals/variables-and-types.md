# Variables and Types

In Python, you don't declare variables. You just assign values to names, and Python figures out the type automatically. This is called **dynamic typing**.

---

## Your First Variables

```python
# These are all valid assignments — no type declaration needed
name = "Alice"
age = 30
height = 5.7
is_student = True
nothing = None
```

That's it. Python sees `"Alice"` and knows it's a string. It sees `30` and knows it's an integer. You never write `int age = 30;` like you would in Java or C.

---

## The Built-In Types

Python has a small set of core types built into the language:

| Type | Examples | Description |
|------|---------|-------------|
| `int` | `0`, `42`, `-7`, `1_000_000` | Whole numbers, any size |
| `float` | `3.14`, `-0.5`, `1.5e10` | Decimal numbers (IEEE 754 double) |
| `complex` | `3+4j`, `1j` | Complex numbers |
| `bool` | `True`, `False` | Boolean — subclass of `int` |
| `str` | `"hello"`, `'world'` | Text (Unicode) |
| `bytes` | `b"data"` | Raw binary data |
| `list` | `[1, 2, 3]` | Ordered, mutable sequence |
| `tuple` | `(1, 2, 3)` | Ordered, immutable sequence |
| `dict` | `{"key": "value"}` | Key-value mapping |
| `set` | `{1, 2, 3}` | Unordered unique values |
| `frozenset` | `frozenset({1, 2})` | Immutable set |
| `NoneType` | `None` | Represents "no value" |

---

## Checking Types

```python
x = 42
print(type(x))          # <class 'int'>
print(isinstance(x, int))  # True

name = "Alice"
print(type(name))       # <class 'str'>

# isinstance is better than type() == ... for checking
# because it handles inheritance properly
print(isinstance(True, int))   # True! bool is a subclass of int
print(type(True) == int)       # False — it's exactly bool
```

---

## Dynamic Typing — Names, Not Variables

Here's the key mental model: in Python, a **name** is just a label stuck on an object. The object has a type; the name doesn't.

```python
x = 42          # name 'x' points to the integer 42
x = "hello"     # name 'x' now points to the string "hello"
x = [1, 2, 3]  # name 'x' now points to a list
```

This is totally valid Python. The type of `x` changed — but really, `x` itself doesn't have a type. The *object* has a type. `x` is just a name pointing at different objects over time.

Compare this to a statically-typed language where a variable's type is fixed at declaration and you'd get a compile error trying to assign an int, then a string, to the same variable.

---

## `id()` — Object Identity

Every object in Python has a unique identity (its memory address in CPython):

```python
a = 42
b = 42
print(id(a))  # e.g. 140235681234560
print(id(b))  # might be the SAME — CPython caches small integers

# For lists, always different objects:
x = [1, 2]
y = [1, 2]
print(id(x) == id(y))  # False — two separate list objects
```

!!! note "Integer caching"
    CPython caches integers from -5 to 256. So `a = 42; b = 42; a is b` is `True` — they literally point to the same object. Above 256, new objects are created each time. Never rely on this in production code; use `==` for value comparison.

---

## Type Conversion (Casting)

You can explicitly convert between types:

```python
# To integer
int("42")       # 42
int(3.9)        # 3 — truncates, does NOT round
int(True)       # 1
int("0xFF", 16) # 255 — base 16

# To float
float("3.14")   # 3.14
float(42)       # 42.0

# To string
str(42)         # "42"
str(3.14)       # "3.14"
str(True)       # "True"

# To bool
bool(0)         # False
bool("")        # False
bool([])        # False — empty list is falsy
bool(None)      # False
bool(42)        # True — any non-zero int is truthy
bool("hello")   # True — non-empty string is truthy
bool([0])       # True — list with one element is truthy
```

---

## Truthiness — Every Object Has a Boolean Value

In Python, every object can be used in a boolean context (like an `if` statement). The rules:

**Falsy values** (evaluate to `False` in boolean context):
- `None`
- `False`
- `0`, `0.0`, `0j` (numeric zeros)
- `""`, `b""` (empty sequences)
- `[]`, `()`, `{}`, `set()` (empty containers)
- Any object whose `__bool__()` returns `False` or `__len__()` returns `0`

**Everything else is truthy.**

```python
items = []
if items:           # False — empty list is falsy
    print("has items")
else:
    print("empty")  # This runs

name = "Alice"
if name:            # True — non-empty string is truthy
    print(f"Hello, {name}")
```

This is why Python code often writes `if items:` instead of `if len(items) > 0:` — they mean the same thing and the first is more idiomatic.

---

## None — The Absence of a Value

`None` is Python's null value. It's a singleton — there's exactly one `None` object in any Python program:

```python
result = None

# The idiomatic way to check for None:
if result is None:
    print("no result yet")

# Never use == for None comparison:
if result == None:   # works but not idiomatic — use 'is'
    pass
```

`None` is what functions return when they don't explicitly return anything:

```python
def do_something():
    x = 1 + 1  # does work but returns nothing

result = do_something()
print(result)   # None
print(type(result))  # <class 'NoneType'>
```

---

## Multiple Assignment and Unpacking

```python
# Assign the same value to multiple names
a = b = c = 0

# Tuple unpacking — assign multiple names at once
x, y = 10, 20
print(x)  # 10
print(y)  # 20

# Swap two variables — clean Python idiom
x, y = y, x

# Extended unpacking (Python 3)
first, *rest = [1, 2, 3, 4, 5]
print(first)  # 1
print(rest)   # [2, 3, 4, 5]

*most, last = [1, 2, 3, 4, 5]
print(most)   # [1, 2, 3, 4]
print(last)   # 5

first, *middle, last = [1, 2, 3, 4, 5]
print(middle) # [2, 3, 4]
```

---

## Naming Conventions (PEP 8)

| Convention | Example | Used for |
|-----------|---------|---------|
| `snake_case` | `my_variable`, `user_name` | Variables, functions, modules |
| `SCREAMING_SNAKE` | `MAX_SIZE`, `PI` | Constants |
| `PascalCase` | `MyClass`, `HttpClient` | Classes |
| `_leading_underscore` | `_internal` | Intended for internal use |
| `__double_leading` | `__mangled` | Name mangling in classes |
| `__dunder__` | `__init__`, `__str__` | Special/magic methods |

!!! tip "Pick meaningful names"
    `user_count` beats `uc`. `calculate_total_price` beats `calc`. Future-you (and your teammates) will thank you.
