# Functional Programming in Python

Python is a multi-paradigm language. While it embraces object-oriented design and procedural scripting, it also provides robust support for **functional programming (FP)**.

Functional programming focuses on:
- Writing **pure functions** (same input always yields same output, zero side effects).
- Treating functions as **first-class citizens**.
- Emphasizing **immutability** and lazy evaluation.
- Composing functions into clean processing pipelines.

---

## First-Class and Higher-Order Functions

In Python, functions are regular objects. You can assign them to variables, pass them as arguments to other functions, store them in data structures, and return them from functions:

```python
def square(x: int) -> int:
    return x * x

def cube(x: int) -> int:
    return x * x * x

# Higher-order function: accepts a function as an argument
def apply_operation(func, value: int) -> int:
    return func(value)

print(apply_operation(square, 5))  # 25
print(apply_operation(cube, 3))    # 27
```

---

## Anonymous Functions: `lambda`

A `lambda` expression creates a small, anonymous function inline. The syntax is:

```python
lambda parameter1, parameter2, ...: expression
```

```python
# Regular function
def multiply(a, b):
    return a * b

# Equivalent lambda expression
multiply_lambda = lambda a, b: a * b

print(multiply(3, 4))         # 12
print(multiply_lambda(3, 4))  # 12
```

### When to Use Lambdas

Lambdas shine when passing short, one-line transformation functions into higher-order utilities:

```python
pairs = [("apple", 5), ("banana", 2), ("cherry", 8), ("date", 1)]

# Sort by count (second element of each tuple)
sorted_pairs = sorted(pairs, key=lambda item: item[1])
print(sorted_pairs)
# [('date', 1), ('banana', 2), ('apple', 5), ('cherry', 8)]
```

!!! tip "Style Recommendation: Don't Bind Lambdas to Names"
    PEP 8 discourages writing `f = lambda x: x * 2`. Use standard `def f(x): return x * 2` instead. Named functions produce better tracebacks and support docstrings and type hints.

---

## Built-in Functional Primitives

### `map(func, iterable)`

Applies `func` to every item in `iterable` lazily, returning an iterator:

```python
numbers = [1, 2, 3, 4, 5]
squares = map(lambda x: x ** 2, numbers)
print(list(squares))  # [1, 4, 9, 16, 25]
```

### `filter(predicate, iterable)`

Yields only items for which `predicate(item)` evaluates to `True`:

```python
numbers = range(10)
evens = filter(lambda x: x % 2 == 0, numbers)
print(list(evens))  # [0, 2, 4, 6, 8]
```

### Map/Filter vs Comprehensions

In idiomatic Python, list and generator comprehensions are almost universally preferred over `map` and `filter` because they are faster, support multiple conditions, and avoid awkward lambdas:

=== "Comprehension Style (Recommended)"
    ```python
    # Map & Filter combined in a single readable line:
    even_squares = [x ** 2 for x in range(10) if x % 2 == 0]
    ```

=== "Functional map/filter Style"
    ```python
    # Requires nested calls and lambdas:
    even_squares = list(map(lambda x: x ** 2, filter(lambda x: x % 2 == 0, range(10))))
    ```

---

## The `operator` Module: Avoiding Lambdas

The standard `operator` module provides efficient C-implemented functions for Python's built-in operators and common attribute/item accessors:

```python
import operator

# Arithmetic functions
print(operator.add(10, 5))      # 15
print(operator.mul(4, 3))       # 12

# itemgetter: extracts item by index or dictionary key
users = [
    {"name": "Alice", "score": 92},
    {"name": "Bob", "score": 78},
    {"name": "Charlie", "score": 95}
]

# Sort by score without a lambda:
by_score = sorted(users, key=operator.itemgetter("score"), reverse=True)
print(by_score[0]["name"])  # Charlie

# attrgetter: extracts attribute from objects
class Point:
    def __init__(self, x, y):
        self.x, self.y = x, y

points = [Point(1, 5), Point(3, 2), Point(2, 8)]
sorted_points = sorted(points, key=operator.attrgetter("x"))
```

---

## Essential `functools` Tools

The `functools` module contains higher-order functions that manipulate or return other functions:

### 1. `functools.reduce()`

Applies a two-argument function cumulatively to iterable items from left to right, reducing them to a single scalar value:

```python
from functools import reduce
import operator

numbers = [1, 2, 3, 4, 5]
# ((1 * 2) * 3) * 4 * 5 = 120
factorial_5 = reduce(operator.mul, numbers)
print(factorial_5)  # 120

# With an initial accumulator value:
total = reduce(lambda acc, x: acc + x, numbers, 100)
print(total)  # 115
```

### 2. `functools.partial()`

Freezes some portion of a function's arguments and/or keywords, resulting in a new callable with a simpler signature:

```python
from functools import partial

def power(base: int, exponent: int) -> int:
    return base ** exponent

# Create specialized functions
square = partial(power, exponent=2)
cube = partial(power, exponent=3)

print(square(9))   # 81
print(cube(4))     # 64
```

### 3. Memoization with `cache` and `lru_cache`

Decorators that store previously computed function results in an in-memory hash table:

```python
from functools import cache

@cache
def fibonacci(n: int) -> int:
    if n < 2:
        return n
    return fibonacci(n - 1) + fibonacci(n - 2)

print(fibonacci(50))  # Computed in microseconds!
```

---

## Function Composition

Function composition combines two or more functions such that the output of one function becomes the input of the next: $f(g(x))$.

```python
from typing import Callable, Any

def compose(*functions: Callable[[Any], Any]) -> Callable[[Any], Any]:
    def composed(initial_value):
        result = initial_value
        # Apply functions in left-to-right order (pipeline)
        for func in functions:
            result = func(result)
        return result
    return composed

# Define small atomic transformations:
strip_text = lambda s: s.strip()
lowercase = lambda s: s.lower()
sanitize = lambda s: s.replace(" ", "_")

# Assemble pipeline
clean_slug = compose(strip_text, lowercase, sanitize)

print(clean_slug("   Hello World from Python   "))
# "hello_world_from_python"
```

---

## Summary of Functional Tools in Python

| Tool | Module | Description |
| :--- | :--- | :--- |
| `map(f, iter)` | Built-in | Transforms each item lazily |
| `filter(p, iter)` | Built-in | Selects matching items lazily |
| `reduce(f, iter)` | `functools` | Folds iterable into a single cumulative value |
| `partial(f, *a)` | `functools` | Pre-fills function arguments |
| `lru_cache(maxsize)` | `functools` | Memoizes function outputs |
| `itemgetter(key)` | `operator` | Fast accessor for index/dictionary lookups |
| `attrgetter(attr)` | `operator` | Fast accessor for object attributes |
| `chain(*iters)` | `itertools` | Chains multiple iterables sequentially |
| `accumulate(iter)` | `itertools` | Running reductions (cumulative sums/products) |
