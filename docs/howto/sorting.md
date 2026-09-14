# Sorting Techniques & Best Practices

Python lists have a built-in `list.sort()` method that modifies the list in-place. Additionally, Python provides the `sorted()` built-in function that builds a new sorted list from any iterable.

Under the hood, Python uses **Timsort** (an adaptive, highly optimized hybrid of merge sort and insertion sort created by Tim Peters) and **Powersort** optimizations. Timsort is guaranteed to be **stable** and completes in $O(N \log N)$ worst-case time and $O(N)$ best-case time on already partially ordered data.

---

## `list.sort()` vs `sorted()`

The primary difference between the two approaches is mutation:

=== "`list.sort()` (In-Place Mutation)"
    ```python
    numbers = [5, 2, 9, 1, 5, 6]
    result = numbers.sort()

    print(numbers)  # [1, 2, 5, 5, 6, 9] (Mutated!)
    print(result)   # None (Always returns None to prevent confusion!)
    ```

=== "`sorted()` (Returns New List)"
    ```python
    numbers = [5, 2, 9, 1, 5, 6]
    ordered = sorted(numbers)

    print(ordered)  # [1, 2, 5, 5, 6, 9] (New list)
    print(numbers)  # [5, 2, 9, 1, 5, 6] (Original remains unchanged)
    ```

!!! note "What Can Be Sorted?"
    `list.sort()` is only available on lists.
    `sorted()` accepts **any iterable**: strings, tuples, dictionaries (sorts keys), sets, generators, or custom iterators.

---

## Key Functions

Both `list.sort()` and `sorted()` take an optional `key` parameter. The key must be a function that takes one argument and returns a key to use for comparison purposes.

### Case-Insensitive String Sorting

```python
words = ["banana", "Apple", "cherry", "Date"]

# Default sort uses ASCII order (capital letters sort before lowercase):
print(sorted(words))
# ['Apple', 'Date', 'banana', 'cherry']

# Sort ignoring case using key=str.lower
print(sorted(words, key=str.lower))
# ['Apple', 'banana', 'cherry', 'Date']
```

### Sorting by Length

```python
phrases = ["elephant", "cat", "hippopotamus", "dog"]
print(sorted(phrases, key=len))
# ['cat', 'dog', 'elephant', 'hippopotamus']
```

---

## Fast Sorting with the `operator` Module

While `lambda` functions work as keys, Python's standard `operator` module provides C-level speedups for extracting items or attributes:

```python
import operator

students = [
    {"name": "Alice", "grade": "A", "age": 22},
    {"name": "Bob", "grade": "B", "age": 19},
    {"name": "Charlie", "grade": "A", "age": 20},
]

# Fast item lookup by dictionary key
by_age = sorted(students, key=operator.itemgetter("age"))
print([s["name"] for s in by_age])
# ['Bob', 'Charlie', 'Alice']
```

For objects with attributes, use `operator.attrgetter()`:

```python
class Employee:
    def __init__(self, name: str, salary: int):
        self.name = name
        self.salary = salary

staff = [Employee("Alice", 95000), Employee("Bob", 62000)]
sorted_staff = sorted(staff, key=operator.attrgetter("salary"), reverse=True)
```

---

## Multi-Field (Composite) Sorting

To sort by multiple criteria (e.g. primary sort by Grade ascending, secondary sort by Age descending), return a tuple from the key function:

```python
students = [
    ("Alice", "B", 22),
    ("Bob", "A", 19),
    ("Charlie", "A", 21),
    ("David", "B", 20),
]

# Sort by Grade ascending (letter), then Age ascending (number):
sorted_students = sorted(students, key=lambda s: (s[1], s[2]))
print(sorted_students)
# [('Bob', 'A', 19), ('Charlie', 'A', 21), ('David', 'B', 20), ('Alice', 'B', 22)]
```

### Inverting Numeric Fields

If you want the primary field ascending and a numeric secondary field descending, negate the numeric field:

```python
# Grade ascending (A before B), but Age descending (oldest first):
sorted_students = sorted(students, key=lambda s: (s[1], -s[2]))
```

---

## Stability of Timsort

Python's sorting algorithm is guaranteed to be **stable**: when multiple records have the same key, their original relative order is strictly preserved.

You can leverage stability to perform multi-pass sorting in reverse order of significance:

```python
data = [("red", 1), ("blue", 2), ("red", 2), ("blue", 1)]

# Pass 1: Sort by number
pass1 = sorted(data, key=lambda x: x[1])
# [('red', 1), ('blue', 1), ('blue', 2), ('red', 2)]

# Pass 2: Sort by color (guaranteed to preserve number ordering within each color!)
pass2 = sorted(pass1, key=lambda x: x[0])
print(pass2)
# [('blue', 1), ('blue', 2), ('red', 1), ('red', 2)]
```

---

## Custom Class Comparisons: Implementing `__lt__`

For custom classes to support sorting without passing an explicit `key=`, implement the `__lt__` (less than) dunder method:

```python
from functools import total_ordering

@total_ordering
class Task:
    def __init__(self, priority: int, title: str):
        self.priority = priority
        self.title = title

    def __eq__(self, other):
        return self.priority == other.priority

    def __lt__(self, other):
        # Higher priority value sorts first
        return self.priority > other.priority

tasks = [Task(1, "Low bug"), Task(5, "Critical outage"), Task(3, "Feature")]
tasks.sort()
print([t.title for t in tasks])
# ['Critical outage', 'Feature', 'Low bug']
```
