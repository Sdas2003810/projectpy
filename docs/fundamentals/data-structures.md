# Data Structures

Python's built-in data structures — lists, tuples, sets, and dicts — are the workhorses of everyday Python programming. This chapter covers them in depth: all the methods, performance characteristics, and idioms you need.

---

## Lists — Deep Dive

You already know that `list` is an ordered, mutable sequence. Here are all the tools:

### All List Methods

```python
fruits = ["apple", "banana", "cherry"]

# Adding elements
fruits.append("date")               # add to end: ["apple","banana","cherry","date"]
fruits.insert(1, "avocado")         # insert at index: ["apple","avocado","banana",...]
fruits.extend(["elderberry", "fig"]) # extend with iterable

# Removing elements
fruits.remove("banana")             # remove first occurrence (ValueError if not found)
popped = fruits.pop()               # remove and return last element
popped_at = fruits.pop(0)           # remove and return at index 0
del fruits[0]                       # delete by index (no return value)
del fruits[1:3]                     # delete a slice
fruits.clear()                      # remove all elements

# Searching
fruits = ["apple", "banana", "cherry", "banana"]
idx = fruits.index("banana")        # 1 — first occurrence index
count = fruits.count("banana")      # 2 — how many times it appears
has_cherry = "cherry" in fruits     # True

# Ordering
fruits.sort()                       # sort in place, ascending
fruits.sort(reverse=True)           # sort descending
fruits.sort(key=len)                # sort by length
fruits.sort(key=str.lower)          # case-insensitive sort

sorted_copy = sorted(fruits)        # returns NEW sorted list (original unchanged)

fruits.reverse()                    # reverse in place

# Copying
copy1 = fruits.copy()               # shallow copy
copy2 = fruits[:]                   # also shallow copy
copy3 = list(fruits)                # also shallow copy

import copy
deep = copy.deepcopy(fruits)        # deep copy (matters for nested structures)
```

### Stacks and Queues

Lists can serve as a **stack** (LIFO — last in, first out) using `append()` and `pop()`:

```python
stack = []
stack.append("first")
stack.append("second")
stack.append("third")
print(stack.pop())    # "third"
print(stack.pop())    # "second"
```

For a **queue** (FIFO — first in, first out), use `collections.deque` — `list.pop(0)` is O(n), while `deque.popleft()` is O(1):

```python
from collections import deque

queue = deque()
queue.append("first")
queue.append("second")
queue.append("third")
print(queue.popleft())   # "first"
print(queue.popleft())   # "second"
```

### List Comprehensions

```python
# [expression for item in iterable if condition]
squares = [x**2 for x in range(10)]
evens = [x for x in range(20) if x % 2 == 0]
flat = [n for row in [[1,2],[3,4],[5,6]] for n in row]  # flatten

# Nested comprehension
matrix = [[i*j for j in range(1, 4)] for i in range(1, 4)]
# [[1, 2, 3], [2, 4, 6], [3, 6, 9]]
```

---

## Tuples

Tuples are immutable sequences. Use them when the data shouldn't change — coordinates, RGB values, database rows, function return values.

```python
point = (3, 4)
rgb = (255, 128, 0)
single = (42,)          # note the comma — (42) is just parentheses around 42

# Packing and unpacking
x, y = point            # unpack into names
a, b, c = rgb

# Tuple methods (only 2)
t = (1, 2, 2, 3, 2)
t.count(2)     # 3
t.index(3)     # 3

# Named tuples — tuples with field names
from collections import namedtuple
Point = namedtuple("Point", ["x", "y"])
p = Point(3, 4)
print(p.x, p.y)         # 3 4
print(p[0], p[1])       # 3 4 — still indexable
```

---

## Sets

Sets are unordered collections of unique elements. The primary use cases: removing duplicates, membership testing, and set operations.

```python
s = {1, 2, 3, 4, 5}
empty = set()           # NOT {} — that's an empty dict

# Basic operations
s.add(6)                # add one element
s.update([7, 8, 9])     # add multiple
s.remove(1)             # remove (KeyError if not present)
s.discard(100)          # remove if present (no error if absent)
s.pop()                 # remove and return an arbitrary element
s.clear()

# Membership (very fast — O(1) average)
print(3 in s)           # True
print(10 not in s)      # True

# Set operations
a = {1, 2, 3, 4}
b = {3, 4, 5, 6}

a | b                   # {1,2,3,4,5,6}  — union
a & b                   # {3, 4}          — intersection
a - b                   # {1, 2}          — difference (in a but not b)
b - a                   # {5, 6}          — difference (in b but not a)
a ^ b                   # {1,2,5,6}       — symmetric difference

# Subset / superset
{1, 2} <= {1, 2, 3}     # True — subset
{1, 2, 3} >= {1, 2}     # True — superset
{1, 2}.isdisjoint({3, 4})  # True — no common elements

# Frozenset — immutable set (can be used as dict key or set element)
fs = frozenset({1, 2, 3})
```

---

## Dictionaries — Deep Dive

Dicts are hash maps — O(1) average for lookup, insert, delete. As of Python 3.7+, they maintain **insertion order**.

```python
d = {"name": "Alice", "age": 30, "city": "London"}

# Access
d["name"]               # "Alice"
d.get("name")           # "Alice"
d.get("missing")        # None (no KeyError)
d.get("missing", "default")  # "default"

# Modify
d["age"] = 31           # update existing
d["email"] = "a@b.com"  # add new key
d.update({"age": 32, "phone": "555-1234"})  # update multiple

# Delete
del d["city"]
removed = d.pop("email")          # removes and returns value
d.pop("nonexistent", None)        # safe pop with default

# Iteration
for key in d:                     # iterates over keys
    print(key)

for key in d.keys():              # explicit (same as above)
    print(key)

for value in d.values():         # just values
    print(value)

for key, value in d.items():     # key-value pairs
    print(f"{key}: {value}")

# Check membership (checks keys)
"name" in d                      # True
"missing" in d                   # False
```

### `setdefault()` and `defaultdict`

```python
# setdefault — set a key only if it doesn't exist
d = {}
d.setdefault("count", 0)
d["count"] += 1       # safe because we know "count" exists

# defaultdict — auto-creates missing keys
from collections import defaultdict

word_count = defaultdict(int)   # default value is int() → 0
for word in "the cat sat on the mat".split():
    word_count[word] += 1       # no KeyError even for new keys

groups = defaultdict(list)
for name, dept in [("Alice", "Eng"), ("Bob", "Eng"), ("Carol", "HR")]:
    groups[dept].append(name)
# defaultdict(list, {'Eng': ['Alice', 'Bob'], 'HR': ['Carol']})
```

### Dict Comprehensions

```python
squares = {x: x**2 for x in range(5)}
# {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}

filtered = {k: v for k, v in d.items() if v > 10}
inverted = {v: k for k, v in d.items()}   # swap keys and values
```

### Merging Dicts

```python
defaults = {"color": "red", "size": "M"}
overrides = {"color": "blue", "weight": 1.5}

# Python 3.9+ — merge operator
merged = defaults | overrides      # {"color": "blue", "size": "M", "weight": 1.5}
defaults |= overrides              # update in place

# Older Python — unpacking
merged = {**defaults, **overrides}
```

---

## Comparing Data Structures

| Feature | list | tuple | set | dict |
|---------|------|-------|-----|------|
| Ordered | Yes | Yes | No | Yes (3.7+) |
| Mutable | Yes | No | Yes | Yes |
| Duplicates | Yes | Yes | No | Keys: No |
| Indexable | Yes | Yes | No | By key |
| Hashable | No | Yes (if contents hashable) | No | No |
| Use as dict key | No | Yes | No | No |
| Lookup speed | O(n) | O(n) | O(1) avg | O(1) avg |

---

## The `collections` Module

Beyond the built-ins, `collections` has specialized containers:

```python
from collections import Counter, OrderedDict, ChainMap

# Counter — count hashable objects
from collections import Counter
c = Counter("mississippi")
print(c)           # Counter({'i': 4, 's': 4, 'p': 2, 'm': 1})
print(c.most_common(2))  # [('i', 4), ('s', 4)]

# ChainMap — single view over multiple dicts
env = ChainMap(os.environ, defaults)
# lookups go through each dict in order

# OrderedDict — insertion order (mostly superseded by dict in 3.7+)
# Still useful for its move_to_end() method
od = OrderedDict([("a", 1), ("b", 2)])
od.move_to_end("a")   # move "a" to end
```
