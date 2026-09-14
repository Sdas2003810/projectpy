# Decorators and Generators

Decorators and generators are advanced Python features for modifying function behavior dynamically and generating sequences of values on demand with minimal memory consumption.

---

## 1. Decorators

A **decorator** is a callable that takes another function as an argument, extends or alters its behavior without modifying the original source code, and returns a modified function.

Writing `@decorator_name` above a function definition:

```python
@my_decorator
def my_function():
    pass
```

is shorthand for:

```python
my_function = my_decorator(my_function)
```

---

## Creating a Decorator with `functools.wraps`

Always use `@functools.wraps` on the wrapper function so that the original function's name and docstring are preserved:

<div class="example-box">
<div class="example-title">Example: Execution Timing Decorator</div>

```python
import time
import functools

def measure_time(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        start = time.perf_counter()
        result = func(*args, **kwargs)
        duration = time.perf_counter() - start
        print(f"Function {func.__name__} took {duration:.4f}s to run.")
        return result
    return wrapper

@measure_time
def compute_sum(n: int) -> int:
    """Calculates sum of numbers up to n."""
    return sum(range(n))

print(compute_sum(1_000_000))
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
Function compute_sum took 0.0152s to run.
499999500000
</div>
</div>

---

## Built-in Decorator: `@functools.lru_cache`

The standard library provides `@functools.lru_cache` for automatic memoization (caching function return values based on input arguments):

```python
from functools import lru_cache

@lru_cache(maxsize=128)
def fibonacci(n: int) -> int:
    if n < 2:
        return n
    return fibonacci(n - 1) + fibonacci(n - 2)

print(fibonacci(50))  # 12586269025 (instant computation)
```

---

## 2. Generators and the `yield` Statement

A **generator function** is a function that contains one or more `yield` statements. When called, it does not execute its body immediately; instead, it returns a generator iterator object.

Each call to `next(generator)` resumes the function until it reaches the next `yield` statement.

<div class="example-box">
<div class="example-title">Example: Simple Number Generator</div>

```python
def count_down(start: int):
    while start > 0:
        yield start
        start -= 1

gen = count_down(3)
print(next(gen))  # 3
print(next(gen))  # 2
print(next(gen))  # 1
# Calling next(gen) again raises StopIteration
```
</div>

---

## Processing Large Files with Generators

Generators allow you to process very large files or continuous data streams line-by-line without loading the entire content into RAM:

```python
def parse_large_file(filepath: str):
    with open(filepath, "r", encoding="utf-8") as f:
        for line in f:
            if line.startswith("ERROR"):
                yield line.strip()

# Only one line exists in memory at any time:
for error_line in parse_large_file("server_log.txt"):
    print(error_line)
```
