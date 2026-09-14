# The Walrus Operator (`:=`)

Introduced in **Python 3.8 (PEP 572)**, the assignment expression operator — colloquially known as the **walrus operator** because `:=` resembles the eyes and tusks of a walrus — allows you to assign values to variables *inside* an expression.

Prior to Python 3.8, assignment was strictly a statement (`x = 5`), meaning it could never be part of a conditional check, loop header, or list comprehension.

---

## Basic Syntax and Mechanism

The syntax is:

```python
NAME := expr
```

The expression `expr` is evaluated, its result is assigned to `NAME`, and that same result is returned to the enclosing expression.

Compare:

=== "With Walrus (`:=`)"
    ```python
    # Evaluates len(data), assigns to `n`, and compares `n > 5` in one step
    if (n := len([1, 2, 3, 4, 5, 6])) > 5:
        print(f"List is too long ({n} elements)")
    ```

=== "Without Walrus (Traditional)"
    ```python
    # Requires an extra statement before the condition
    n = len([1, 2, 3, 4, 5, 6])
    if n > 5:
        print(f"List is too long ({n} elements)")
    ```

---

## Common Idioms and Real-World Patterns

### 1. The `while` Loop Read Pattern

A classic use case is reading data in chunks (from files, network sockets, or generator streams) until an empty value is reached:

=== "With Walrus (Clean)"
    ```python
    with open("server.log", "r", encoding="utf-8") as f:
        while chunk := f.read(1024):
            process_chunk(chunk)
    ```

=== "Without Walrus (Duplicated Read or `while True`)"
    ```python
    # Approach A: Duplicated read call
    with open("server.log", "r", encoding="utf-8") as f:
        chunk = f.read(1024)
        while chunk:
            process_chunk(chunk)
            chunk = f.read(1024)

    # Approach B: Infinite loop with break
    with open("server.log", "r", encoding="utf-8") as f:
        while True:
            chunk = f.read(1024)
            if not chunk:
                break
            process_chunk(chunk)
    ```

### 2. Regular Expression Matching

When parsing lines with regex, you frequently need to check if a pattern matches and immediately extract its captured groups:

```python
import re

log_line = "2026-09-14 14:05:00 [ERROR] Connection timeout to database:5432"

if match := re.search(r"\[(ERROR|WARNING)\] (.+)", log_line):
    level, message = match.group(1), match.group(2)
    print(f"Alert level: {level} -> Message: {message}")
```

<div class="terminal-output">
Alert level: ERROR -> Message: Connection timeout to database:5432
</div>

### 3. Avoiding Redundant Computation in Comprehensions

In list comprehensions, computing an expensive function both in the `if` filter and the projection expression leads to double execution:

=== "With Walrus (Single Execution)"
    ```python
    import math

    def expensive_transform(x: float) -> float:
        return math.sin(x) ** 2 + math.cos(x) ** 3

    raw_values = [0.1, 0.5, 1.2, 2.4, 3.1]

    # expensive_transform is only called once per item!
    results = [y for x in raw_values if (y := expensive_transform(x)) > 0.5]
    ```

=== "Without Walrus (Double Calculation or Nested Comp)"
    ```python
    # Double calculation: slow!
    results = [expensive_transform(x) for x in raw_values if expensive_transform(x) > 0.5]

    # Or awkward nested comprehension:
    results = [y for y in (expensive_transform(x) for x in raw_values) if y > 0.5]
    ```

---

## Operator Precedence and Parentheses

The walrus operator has a very low precedence — lower than comparison operators (`==`, `<`, `>`, etc.), arithmetic operators (`+`, `*`), and logical operators (`and`, `or`, `not`).

Because of this, **parentheses are almost always required** when using `:=` inside conditions:

```python
# WRONG: `len(data) > 0` evaluates first, assigning a boolean (True) to `n`!
# if n := len(data) > 0:
#     print(n)  # Prints True, NOT the length!

# CORRECT: Wrap the assignment in parentheses
if (n := len(data)) > 0:
    print(f"Count: {n}")
```

---

## Scope of Walrus Variables

Variables assigned with `:=` obey standard Python scoping rules:
- If assigned inside a function, the variable belongs to that function's local scope.
- In comprehensions, the variable leaks into the **enclosing function scope** (unlike normal comprehension loop variables which are strictly private to the comprehension scope):

```python
def example():
    total = sum((last := x * 2) for x in range(5))
    print(f"Last processed value: {last}")  # Valid! `last` is 8

example()
```

---

## Syntax Restrictions

PEP 572 intentionally restricts where `:=` can be used to prevent unreadable code:

1. **Cannot assign directly to attributes**:
    ```python
    # SyntaxError: cannot use assignment expressions with attribute
    # (self.x := 10)
    ```
2. **Cannot assign directly to subscripts**:
    ```python
    # SyntaxError: cannot use assignment expressions with subscript
    # (my_dict["key"] := 10)
    ```
3. **Cannot be used for multiple unpacking**:
    ```python
    # SyntaxError
    # (x, y := 1, 2)
    ```
4. **Cannot be used as an unparenthesized statement at statement level**:
    ```python
    # SyntaxError: invalid syntax
    # x := 10
    # Use regular `x = 10` instead
    ```

---

## Best Practice: Readability First

The walrus operator is a tool for clarity, not code golf.

!!! tip "When to Use"
    - When it eliminates repetitive function calls or nested loops.
    - When reading data streams (`while chunk := ...`).
    - When capturing regex matches in conditionals.

!!! warning "When to Avoid"
    - Avoid chaining multiple `:=` in one statement.
    - If someone reading your code has to pause and mentally parse the assignment flow, rewrite it as separate statements.
