# Functional Programming HOWTO

This guide provides a comprehensive walkthrough of functional programming patterns in Python, focusing on iterators, generator pipelines, and the standard functional modules `itertools` and `functools`.

---

## The Philosophy of Functional Code

In purely functional languages (like Haskell or Clojure), programs consist entirely of mathematical functions with no mutable state or side effects.

While Python does not force pure functional programming, adopting functional idioms yields:
- **Modularity**: Small, single-purpose transformation functions that are easy to test.
- **Lazy Evaluation**: Processing massive streams and files without exhausting memory.
- **Concurrency Safety**: Immutable structures eliminate race conditions.

---

## Lazy Generator Pipelines

The most practical application of functional programming in Python is building **lazy processing pipelines** using generator expressions.

Imagine processing a 50GB web server access log. Loading the file into a list crashes your server with an `OutOfMemoryError`. Using functional generators, data flows item-by-item through the pipeline using constant memory:

```python
def read_log_lines(file_path: str):
    """Generator: yields one line at a time from disk."""
    with open(file_path, "r", encoding="utf-8") as f:
        for line in f:
            yield line

# Pipeline Stage 1: Strip whitespace
lines = (line.strip() for line in read_log_lines("access.log"))

# Pipeline Stage 2: Filter for error entries only
errors = (line for line in lines if "ERROR" in line)

# Pipeline Stage 3: Extract IP addresses
import re
ip_regex = re.compile(r"\b(?:\d{1,3}\.){3}\d{1,3}\b")
ips = (m.group(0) for line in errors if (m := ip_regex.search(line)))

# Pipeline Stage 4: Consume results (only now does computation actually run!)
for ip in ips:
    print(f"Malicious actor detected from: {ip}")
```

---

## Mastering `itertools` for Functional Operations

The `itertools` module provides fast, memory-efficient building blocks for iterator manipulation:

### 1. Infinite Iterators

```python
import itertools

# count(start, step): infinite arithmetic progression
counter = itertools.count(start=100, step=5)
print(next(counter))  # 100
print(next(counter))  # 105

# cycle(iterable): cycles endlessly through elements
traffic_lights = itertools.cycle(["RED", "GREEN", "YELLOW"])

# repeat(elem, [n]): repeats an element n times (or infinitely)
zeros = itertools.repeat(0, times=3)
print(list(zeros))  # [0, 0, 0]
```

### 2. Slicing Iterators with `islice()`

Standard slice notation (`items[10:20]`) requires a list. `itertools.islice()` slices any generator or iterator lazily without converting it into a list:

```python
import itertools

# Take the first 5 elements from an infinite counter:
first_five = list(itertools.islice(itertools.count(1), 5))
print(first_five)  # [1, 2, 3, 4, 5]
```

### 3. Combining Iterables with `chain()`

Chains multiple iterables sequentially into a single continuous stream:

```python
import itertools

frontend_team = ["Alice", "Bob"]
backend_team = ["Charlie", "Diana"]
devops_team = ["Eve"]

all_engineers = itertools.chain(frontend_team, backend_team, devops_team)
print(list(all_engineers))
# ['Alice', 'Bob', 'Charlie', 'Diana', 'Eve']
```

### 4. Grouping Data with `groupby()`

Groups consecutive keys in an iterable.

!!! warning "`groupby()` Requires Sorted Input"
    `itertools.groupby()` only groups *consecutive* matching elements. You must sort the iterable by the grouping key first!

```python
import itertools

records = [
    {"dept": "Engineering", "user": "Alice"},
    {"dept": "Design", "user": "Bob"},
    {"dept": "Engineering", "user": "Charlie"},
    {"dept": "Design", "user": "David"},
]

# Step 1: Sort by grouping key
records.sort(key=lambda r: r["dept"])

# Step 2: Group
for dept, group in itertools.groupby(records, key=lambda r: r["dept"]):
    users = [r["user"] for r in group]
    print(f"{dept}: {users}")
```

<div class="terminal-output">
Design: ['Bob', 'David']
Engineering: ['Alice', 'Charlie']
</div>

### 5. Combinatorics: `product`, `permutations`, `combinations`

```python
import itertools

# Cartesian Product
suits = ["H", "D"]
ranks = ["A", "K"]
deck = list(itertools.product(suits, ranks))
# [('H', 'A'), ('H', 'K'), ('D', 'A'), ('D', 'K')]

# Combinations (order does NOT matter, no replacement)
pairs = list(itertools.combinations(["A", "B", "C"], 2))
# [('A', 'B'), ('A', 'C'), ('B', 'C')]

# Permutations (order matters)
orders = list(itertools.permutations(["A", "B", "C"], 2))
# [('A', 'B'), ('A', 'C'), ('B', 'A'), ('B', 'C'), ('C', 'A'), ('C', 'B')]
```

---

## Cumulative Transformations with `itertools.accumulate()`

Produces running totals or accumulated results:

```python
import itertools
import operator

numbers = [1, 2, 3, 4, 5]

# Running cumulative sum
running_sum = list(itertools.accumulate(numbers))
print(running_sum)  # [1, 3, 6, 10, 15]

# Running cumulative product
running_product = list(itertools.accumulate(numbers, operator.mul))
print(running_product)  # [1, 2, 6, 24, 120]
```
