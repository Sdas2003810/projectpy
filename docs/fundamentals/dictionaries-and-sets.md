# Dictionaries and Sets

Python provides two built-in hash-based collection types: **Dictionaries** for key-value pairs, and **Sets** for unique collections. Both provide average $O(1)$ time complexity for lookups and insertions.

---

## 1. Dictionaries (`dict`)

A dictionary maps unique keys to values. Dictionaries are mutable and preserve insertion order (Python 3.7+).

### Creating Dictionaries

```python
# Key-value syntax using curly braces
student = {
    "id": 101,
    "name": "Alice",
    "major": "Computer Science",
    "gpa": 3.8
}

# Empty dictionary
empty_dict = {}
# or empty_dict = dict()
```

### Accessing Values Safely (`.get()`)

Accessing a nonexistent key directly with square brackets raises a `KeyError`. Use `.get()` to provide a default fallback:

<div class="example-box">
<div class="example-title">Example: Accessing Dictionary Keys</div>

```python
config = {"host": "localhost", "port": 8080}

# Safe access with default
timeout = config.get("timeout", 30)
print("Timeout:", timeout)  # 30
```
</div>

### Common Dictionary Methods

| Method | Description | Example |
| :--- | :--- | :--- |
| `d.get(key, default)` | Returns value for key, or default if not found | `d.get('name', 'N/A')` |
| `d.keys()` | Returns a view of dictionary keys | `d.keys()` |
| `d.values()` | Returns a view of dictionary values | `d.values()` |
| `d.items()` | Returns a view of `(key, value)` pairs | `d.items()` |
| `d.update(other)` | Updates dictionary with elements from other | `d.update({'port': 9000})` |
| `d.pop(key[, default])`| Removes specified key and returns value | `d.pop('host')` |
| `d.clear()` | Removes all elements | `d.clear()` |

### Merging Dictionaries (Python 3.9+ Union Operator `|`)

```python
defaults = {"theme": "light", "language": "en"}
user_pref = {"theme": "dark"}

# Merge using | operator
merged = defaults | user_pref
print(merged)  # {'theme': 'dark', 'language': 'en'}
```

---

## 2. Sets (`set`)

A set is an unordered collection of distinct (unique) hashable elements. Duplicate entries are automatically eliminated.

### Creating Sets

```python
# Using curly braces with values (not key-value pairs)
prime_set = {2, 3, 5, 7, 2, 3}
print(prime_set)  # {2, 3, 5, 7}

# NOTE: An empty set MUST be created using set(), not {} (which creates a dict)
empty_set = set()
```

### Removing Duplicates from a Sequence

```python
raw_numbers = [1, 2, 2, 3, 4, 4, 4, 5]
unique_numbers = list(set(raw_numbers))
print(unique_numbers)  # [1, 2, 3, 4, 5]
```

### Mathematical Set Operations

| Operation | Operator | Method | Description |
| :--- | :---: | :--- | :--- |
| **Union** | `A | B` | `A.union(B)` | Elements present in either A, B, or both |
| **Intersection** | `A & B` | `A.intersection(B)` | Elements present in BOTH A and B |
| **Difference** | `A - B` | `A.difference(B)` | Elements in A that are NOT in B |
| **Symmetric Diff**| `A ^ B` | `A.symmetric_difference(B)` | Elements in either A or B, but NOT both |

<div class="example-box">
<div class="example-title">Example: Set Operations</div>

```python
set_a = {1, 2, 3, 4}
set_b = {3, 4, 5, 6}

print("Union (|):             ", set_a | set_b)
print("Intersection (&):      ", set_a & set_b)
print("Difference (A - B):    ", set_a - set_b)
print("Symmetric Difference (^):", set_a ^ set_b)
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
Union (|):              {1, 2, 3, 4, 5, 6}
Intersection (&):       {3, 4}
Difference (A - B):     {1, 2}
Symmetric Difference (^): {1, 2, 5, 6}
</div>
</div>

---

## 3. Frozen Sets (`frozenset`)

A `frozenset` is an immutable version of a Python set. Because it is immutable and hashable, it can be used as a dictionary key:

```python
fixed_permissions = frozenset(["read", "write"])
user_roles = {fixed_permissions: "Editor"}
```
