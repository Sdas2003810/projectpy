# `functools` — Higher-Order Functions & Decorators

The `functools` module contains functions that operate on or return other functions. It is one of Python's most essential standard libraries for performance optimization, clean decorator authoring, and functional patterns.

---

## Preserving Function Metadata: `@wraps`

When you write a custom decorator, replacing the original function with an inner wrapper clobbers the function's `__name__`, `__doc__`, annotations, and signature.

`@functools.wraps` copies all introspection attributes from the decorated function onto the wrapper:

=== "With `@wraps` (Correct)"
    ```python
    import functools

    def logger(func):
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            print(f"Calling {func.__name__}")
            return func(*args, **kwargs)
        return wrapper

    @logger
    def calculate_tax(subtotal: float) -> float:
        """Calculate standard 8.5% sales tax."""
        return subtotal * 0.085

    print(calculate_tax.__name__)  # "calculate_tax"
    print(calculate_tax.__doc__)   # "Calculate standard 8.5% sales tax."
    ```

=== "Without `@wraps` (Metadata Lost)"
    ```python
    def bad_logger(func):
        def wrapper(*args, **kwargs):
            return func(*args, **kwargs)
        return wrapper

    @bad_logger
    def calculate_tax(subtotal: float) -> float:
        """Calculate standard 8.5% sales tax."""
        return subtotal * 0.085

    print(calculate_tax.__name__)  # "wrapper" (Bug: original name lost!)
    print(calculate_tax.__doc__)   # None
    ```

---

## Function Caching & Memoization

### `@cache` (Python 3.9+)

`@functools.cache` provides unbounded memoization. It stores every unique set of arguments and their computed return value in an internal hash table:

```python
import functools
import time

@functools.cache
def compute_heavy_metric(n: int) -> int:
    time.sleep(0.5)  # Simulate expensive database/CPU calculation
    return n * 100

print(compute_heavy_metric(5))  # Takes 0.5s
print(compute_heavy_metric(5))  # Instant: served from cache
```

### `@lru_cache` (Bounded Cache)

For production services where memory usage must be capped, use `@functools.lru_cache(maxsize=128)`:

```python
@functools.lru_cache(maxsize=256, typed=False)
def fetch_user_profile(user_id: int):
    # Fetch from external API
    return {"id": user_id, "active": True}

# Inspect cache statistics
print(fetch_user_profile.cache_info())
# CacheInfo(hits=0, misses=1, maxsize=256, currsize=1)

# Clear cache when stale
fetch_user_profile.cache_clear()
```

### `@cached_property` (Python 3.8+)

Caches an expensive instance method result directly onto the instance's `__dict__`. The result is computed once on first access and reused for the lifetime of that instance:

```python
class DataAnalyzer:
    def __init__(self, raw_data: list[int]):
        self.raw_data = raw_data

    @functools.cached_property
    def variance(self) -> float:
        print("Calculating variance (expensive)...")
        mean = sum(self.raw_data) / len(self.raw_data)
        return sum((x - mean) ** 2 for x in self.raw_data) / len(self.raw_data)

analyzer = DataAnalyzer([10, 20, 30, 40, 50])
print(analyzer.variance)  # Runs calculation
print(analyzer.variance)  # Instant: read from analyzer.__dict__["variance"]
```

---

## Partial Function Application: `partial()`

`functools.partial` freezes a portion of a function's arguments, creating a new callable with fewer required parameters:

```python
from functools import partial

def send_email(sender: str, recipient: str, subject: str, body: str):
    return f"From: {sender} | To: {recipient} | Subject: {subject} | Body: {body}"

# Create a specialized alert sender for system notifications
send_system_alert = partial(
    send_email,
    "system-daemon@company.org",
    subject="CRITICAL ALERT"
)

# Call with remaining arguments:
msg = send_system_alert(
    recipient="oncall@company.org",
    body="Database disk usage exceeded 90%."
)
print(msg)
```

---

## Single Dispatch Generic Functions: `@singledispatch`

Python does not support traditional method overloading by type. However, `@functools.singledispatch` implements polymorphic single-dispatch functions based on the type of the first argument:

```python
from functools import singledispatch

@singledispatch
def serialize(value):
    raise NotImplementedError(f"Unsupported type: {type(value)}")

@serialize.register(int)
@serialize.register(float)
def _(value):
    return f"<Numeric: {value}>"

@serialize.register(str)
def _(value):
    return f"<String: '{value}'>"

@serialize.register(list)
def _(value):
    items = ", ".join(serialize(x) for x in value)
    return f"[{items}]"

print(serialize(42))
print(serialize("antigravity"))
print(serialize([1, "two", 3.0]))
```

<div class="terminal-output">
<Numeric: 42>
<String: 'antigravity'>
[<Numeric: 1>, <String: 'two'>, <Numeric: 3.0>]
</div>

For class methods, use `@functools.singledispatchmethod`.

---

## Generating Rich Comparisons: `@total_ordering`

Instead of manually defining all 6 comparison dunder methods (`__eq__`, `__ne__`, `__lt__`, `__le__`, `__gt__`, `__ge__`), implement `__eq__` and just **one** ordering method (e.g. `__lt__`), and decorate with `@functools.total_ordering`:

```python
from functools import total_ordering

@total_ordering
class Student:
    def __init__(self, name: str, gpa: float):
        self.name = name
        self.gpa = gpa

    def __eq__(self, other):
        if not isinstance(other, Student):
            return NotImplemented
        return self.gpa == other.gpa

    def __lt__(self, other):
        if not isinstance(other, Student):
            return NotImplemented
        return self.gpa < other.gpa

alice = Student("Alice", 3.9)
bob = Student("Bob", 3.7)

# All comparison operators are synthesized automatically:
print(alice > bob)   # True
print(bob <= alice)  # True
print(alice >= bob)  # True
```

---

## Summary of `functools` Utilities

| Utility | Description | Introduced |
| :--- | :--- | :--- |
| `@wraps(func)` | Preserves function identity and docstrings in decorators | Python 2.5 |
| `partial(f, *a, **k)` | Freezes function arguments | Python 2.5 |
| `@lru_cache(maxsize)` | Memoization with Least-Recently-Used eviction | Python 3.2 |
| `@cache` | Unbounded memoization (faster `lru_cache(None)`) | Python 3.9 |
| `@cached_property` | Lazily evaluates and stores property on instance | Python 3.8 |
| `@singledispatch` | Function overloading by type of first argument | Python 3.4 |
| `@total_ordering` | Auto-generates remaining comparison dunders | Python 2.7 / 3.2 |
| `reduce(f, iter)` | Cumulative folding of iterable into scalar | Built-in in 2.x, `functools` in 3.x |
