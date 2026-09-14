# Python Quick Syntax Cheat Sheet

A condensed reference for rapid syntax, method, and function lookups.

---

## Basic Data Types

| Type | Example | Mutable? | Description |
| :--- | :--- | :---: | :--- |
| `int` | `42` | No | Arbitrary-precision integer |
| `float` | `3.14159` | No | IEEE 754 double precision floating point |
| `str` | `"Hello"` | No | Unicode character sequence |
| `bool` | `True`, `False` | No | Boolean flag |
| `list` | `[1, 2, "three"]` | **Yes** | Ordered heterogeneous sequence |
| `tuple`| `(1, 2, "three")` | No | Ordered immutable sequence |
| `dict` | `{"id": 1, "name": "Ada"}` | **Yes** | Key-value hash map |
| `set` | `{"red", "blue"}` | **Yes** | Unordered collection of unique items |
| `NoneType` | `None` | No | Absence of a value (singleton) |

---

## String Formatting and Slicing

```python
# F-Strings
name = "Ada"
pi = 3.14159
print(f"User: {name}, Pi: {pi:.2f}")

# Slicing: [start:stop:step]
s = "Hello, Python!"
s[0:5]    # 'Hello'
s[:5]     # 'Hello'
s[7:]     # 'Python!'
s[::-1]   # '!nohtyP ,olleH' (Reverse)
```

---

## Core Sequence Operations

=== "Lists"
    ```python
    lst = [1, 2, 3]
    lst.append(4)          # [1, 2, 3, 4]
    lst.extend([5, 6])     # [1, 2, 3, 4, 5, 6]
    lst.insert(0, 99)      # [99, 1, 2, 3, 4, 5, 6]
    lst.pop()              # Removes & returns last item
    lst.remove(99)         # Removes first occurrence of 99
    lst.sort()             # In-place ascending sort
    len(lst)               # Count of items
    ```

=== "Dictionaries"
    ```python
    d = {"a": 1, "b": 2}
    d["c"] = 3             # Add or update
    val = d.get("missing", 0) # Safe get with default fallback
    del d["a"]             # Delete key
    keys = list(d.keys())  # ['b', 'c']
    vals = list(d.values())# [2, 3]
    items = list(d.items())# [('b', 2), ('c', 3)]
    ```

=== "Sets"
    ```python
    s1 = {1, 2, 3}
    s2 = {3, 4, 5}
    s1 | s2                # Union: {1, 2, 3, 4, 5}
    s1 & s2                # Intersection: {3}
    s1 - s2                # Difference: {1, 2}
    s1 ^ s2                # Symmetric Difference: {1, 2, 4, 5}
    ```

---

## Essential Built-in Functions

| Function | What it Does | Example |
| :--- | :--- | :--- |
| `len(seq)` | Returns length of sequence | `len("cat") -> 3` |
| `range(stop)` | Generates range of integers | `list(range(3)) -> [0, 1, 2]` |
| `enumerate(iter)`| Yields `(index, value)` tuples | `for i, v in enumerate(lst):` |
| `zip(iter1, iter2)`| Combines iterables in parallel | `dict(zip(["a", "b"], [1, 2]))` |
| `sorted(iter)` | Returns a new sorted list | `sorted([3, 1, 2]) -> [1, 2, 3]` |
| `isinstance(x, T)`| Checks if object is instance of type | `isinstance("hi", str) -> True` |
| `any(iterable)` | Returns `True` if any element is truthy | `any([0, False, 5]) -> True` |
| `all(iterable)` | Returns `True` if ALL elements are truthy | `all([True, 1, "yes"]) -> True` |

---

## The Zen of Python (PEP 20)

Type `import this` in any Python terminal:

```text
Beautiful is better than ugly.
Explicit is better than implicit.
Simple is better than complex.
Complex is better than complicated.
Flat is better than nested.
Sparse is better than dense.
Readability counts.
Special cases aren't special enough to break the rules.
Although practicality beats purity.
Errors should never pass silently.
Unless explicitly silenced.
In the face of ambiguity, refuse the temptation to guess.
There should be one-- and preferably only one --obvious way to do it.
Now is better than never.
Although never is often better than *right* now.
If the implementation is hard to explain, it's a bad idea.
If the implementation is easy to explain, it may be a good idea.
```
