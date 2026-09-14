# Expressions

An expression is any piece of code that produces a value. In Python, almost everything you write is either a statement (does something) or an expression (produces a value). Sometimes expressions *are* statements — `f()` is a valid statement.

---

## Atoms — The Simplest Expressions

Atoms are the simplest building blocks:

| Atom | Examples |
|------|---------|
| Identifiers | `x`, `my_variable`, `print` |
| Literals | `42`, `3.14`, `"hello"`, `b"bytes"`, `True`, `None` |
| Parenthesized expressions | `(1 + 2)` |
| Tuple display | `(1, 2, 3)` or `1, 2, 3` |
| List display | `[1, 2, 3]`, `[x**2 for x in range(5)]` |
| Set display | `{1, 2, 3}`, `{x for x in range(5)}` |
| Dict display | `{"a": 1}`, `{k: v for k, v in items}` |
| Generator expression | `(x**2 for x in range(5))` |
| `...` (Ellipsis) | Used as a placeholder or in type hints |
| `yield` | Only valid in a generator function |

---

## Attribute Access

```python
# obj.attr
"hello".upper      # attribute lookup
[1,2,3].append     # method lookup
import os
os.path.join       # chained attribute lookup
```

---

## Subscript / Indexing

```python
my_list[0]         # integer index
my_dict["key"]     # dict key
my_str[1:5]        # slice
my_tensor[0, 1, 2] # multiple indices (numpy-style)
```

Python calls `__getitem__` for indexing. Slices call `__getitem__` with a `slice` object.

---

## Calls

```python
# Function call
print("hello")

# Method call
"hello".upper()

# With *args and **kwargs unpacking
def f(a, b, c): ...
args = (1, 2, 3)
f(*args)           # same as f(1, 2, 3)

kwargs = {"a": 1, "b": 2, "c": 3}
f(**kwargs)        # same as f(a=1, b=2, c=3)
```

---

## Arithmetic Operators

```python
5 + 2    # 7  — addition
5 - 2    # 3  — subtraction
5 * 2    # 10 — multiplication
5 / 2    # 2.5 — true division (always float)
5 // 2   # 2  — floor division (rounds toward -infinity)
5 % 2    # 1  — modulo
5 ** 2   # 25 — exponentiation
-5 // 2  # -3 — floor division rounds toward -infinity, NOT toward 0
```

!!! warning "`//` rounds toward negative infinity"
    `7 // 2` is `3`, but `-7 // 2` is `-4` (not `-3`). If you want truncation toward zero, use `int(a / b)` or `math.trunc(a / b)`.

---

## Bitwise Operators

```python
a = 0b1010  # 10
b = 0b1100  # 12

a & b   # 0b1000 = 8  — AND
a | b   # 0b1110 = 14 — OR
a ^ b   # 0b0110 = 6  — XOR
~a      # -11          — NOT (two's complement)
a << 1  # 0b10100 = 20 — left shift
a >> 1  # 0b0101 = 5  — right shift
```

---

## Comparison Operators

```python
x == y   # equal
x != y   # not equal
x < y    # less than
x > y    # greater than
x <= y   # less than or equal
x >= y   # greater than or equal
x is y   # same object identity
x is not y
x in y   # x is a member of y
x not in y
```

Comparisons can be **chained**: `0 < x < 10` is equivalent to `0 < x and x < 10`.

---

## Boolean Operators

`and` and `or` are **short-circuit** operators and return an operand, not necessarily `True` or `False`:

```python
# 'and' returns the first falsy value, or the last value if all are truthy
print(0 and "hello")   # 0    (short-circuits at 0)
print(1 and "hello")   # "hello" (both truthy, returns last)

# 'or' returns the first truthy value, or the last value if all are falsy
print(0 or "hello")    # "hello"
print("" or 0 or None) # None (all falsy, returns last)

# Practical idiom: default value
username = user_input or "Guest"
```

---

## Conditional Expression (Ternary)

```python
# value_if_true if condition else value_if_false
x = 10
label = "even" if x % 2 == 0 else "odd"
print(label)  # even
```

---

## Lambda Expressions

Anonymous functions defined with `lambda`:

```python
square = lambda x: x ** 2
add = lambda x, y: x + y

# Most useful as callbacks
numbers = [3, 1, 4, 1, 5, 9]
numbers.sort(key=lambda x: -x)   # sort descending
print(numbers)   # [9, 5, 4, 3, 1, 1]
```

Lambda is limited to a single expression. For anything more complex, use a named `def`.

---

## Assignment Expressions (Walrus `:=`) — Python 3.8+

`:=` assigns a value to a name *as part of an expression*:

```python
# Without walrus — must call function twice or use temp variable
data = get_data()
if data:
    process(data)

# With walrus — cleaner
if data := get_data():
    process(data)

# Very useful in while loops
while chunk := file.read(8192):
    process(chunk)
```

See the [Walrus Operator guide](../advanced/walrus-operator.md) for detailed usage.

---

## Starred Expressions

`*` unpacks iterables and `**` unpacks dicts in expressions:

```python
a = [1, 2, 3]
b = [4, 5, 6]

combined = [*a, *b]         # [1, 2, 3, 4, 5, 6]
d1 = {"x": 1}
d2 = {"y": 2}
merged = {**d1, **d2}       # {"x": 1, "y": 2}

first, *rest = [1, 2, 3, 4]  # first=1, rest=[2,3,4]
```

---

## Operator Precedence (Highest to Lowest)

| Operators | Description |
|-----------|-------------|
| `(...)`, `[...]`, `{...}` | Parentheses, brackets |
| `x[i]`, `x[i:j]`, `f(args)`, `x.attr` | Subscript, call, attribute |
| `await x` | Await expression |
| `**` | Exponentiation |
| `+x`, `-x`, `~x` | Unary plus, minus, bitwise NOT |
| `*`, `@`, `/`, `//`, `%` | Multiplication, matrix multiply, division |
| `+`, `-` | Addition, subtraction |
| `<<`, `>>` | Bit shifts |
| `&` | Bitwise AND |
| `^` | Bitwise XOR |
| `\|` | Bitwise OR |
| `in`, `not in`, `is`, `is not`, `<`, `<=`, `>`, `>=`, `!=`, `==` | Comparisons |
| `not x` | Boolean NOT |
| `and` | Boolean AND |
| `or` | Boolean OR |
| `if – else` | Conditional expression |
| `lambda` | Lambda |
| `:=` | Walrus (assignment expression) |
