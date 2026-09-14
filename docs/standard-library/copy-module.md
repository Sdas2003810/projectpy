# Object Copying & Modification: `copy` Module

In Python, assignment statements (`b = a`) do not copy objects; they merely bind a new name to the existing object in memory. When dealing with mutable collections (lists, dictionaries, sets) or complex custom objects, understanding the difference between reference sharing, shallow copying, and deep copying is essential.

The standard library's `copy` module provides generic shallow and deep copy operations, and starting in **Python 3.13**, the new `copy.replace()` function provides a unified mechanism to produce modified copies of objects.

---

## Assignment vs Shallow Copy vs Deep Copy

```python
import copy

original = [[1, 2, 3], [4, 5, 6]]

# 1. Assignment (Alias)
alias = original

# 2. Shallow Copy
shallow = copy.copy(original)  # or original.copy() or list(original)

# 3. Deep Copy
deep = copy.deepcopy(original)
```

### Memory Structure Comparison

```text
1. Assignment (alias = original):
   alias -----\
               +---> [ [1, 2, 3], [4, 5, 6] ]
   original --/

2. Shallow Copy (shallow = copy.copy(original)):
   original ---> [ [1, 2, 3], [4, 5, 6] ]
                      ^          ^
                      |          |  (Inner lists are SHARED)
   shallow  ---> [ ---+----------+ ]

3. Deep Copy (deep = copy.deepcopy(original)):
   original ---> [ [1, 2, 3], [4, 5, 6] ]
   deep     ---> [ [1, 2, 3], [4, 5, 6] ]  (Completely independent in memory)
```

### Behavioral Differences

```python
# Modifying the top-level list container:
original.append([7, 8, 9])
print(len(shallow))  # 2 (shallow copy has its own outer list)
print(len(alias))    # 3 (alias reflects changes to the outer list)

# Modifying a nested mutable element:
original[0][0] = 999
print(shallow[0][0]) # 999 (affected! Inner list was referenced, not copied)
print(deep[0][0])    # 1   (unaffected! Inner list was cloned recursively)
```

---

## The `copy.copy()` Function (Shallow Copy)

`copy.copy(x)` creates a new compound object and inserts references into it to the objects found in the original:

```python
import copy

profile = {
    "user_id": 101,
    "tags": ["python", "backend"],
    "settings": {"dark_mode": True}
}

profile_copy = copy.copy(profile)

# Modifying an immutable top-level value
profile_copy["user_id"] = 202
print(profile["user_id"])  # 101 (original remains untouched)

# Modifying a nested mutable collection
profile_copy["tags"].append("developer")
print(profile["tags"])  # ['python', 'backend', 'developer'] (affected!)
```

---

## The `copy.deepcopy()` Function (Deep Copy)

`copy.deepcopy(x)` creates a new compound object and then, recursively, inserts copies into it of the objects found in the original:

```python
import copy

user_state = {
    "id": 42,
    "permissions": ["read", "write"],
    "sessions": [{"token": "abc", "active": True}]
}

cloned_state = copy.deepcopy(user_state)

# Modify nested objects without impacting the original
cloned_state["permissions"].append("admin")
cloned_state["sessions"][0]["active"] = False

print(user_state["permissions"])         # ['read', 'write']
print(user_state["sessions"][0]["active"]) # True
```

### Handling Circular References
`copy.deepcopy()` automatically tracks objects it has already copied using an internal memo dictionary. It safely handles self-referential or cyclic data structures without falling into infinite recursion:

```python
import copy

node_a = {"name": "A"}
node_b = {"name": "B"}

# Create circular references
node_a["neighbor"] = node_b
node_b["neighbor"] = node_a

# Deepcopy safely resolves the cycle
copied_a = copy.deepcopy(node_a)
print(copied_a["neighbor"]["neighbor"] is copied_a)  # True
```

---

## Custom Copy Behavior: `__copy__` and `__deepcopy__`

Classes can define custom copy semantics by implementing the special `__copy__()` and `__deepcopy__(memo)` methods:

```python
import copy

class DatabaseConnection:
    def __init__(self, host: str, port: int):
        self.host = host
        self.port = port
        self.is_connected = True

    def __copy__(self):
        # A shallow copy gets the same connection parameters, but disconnected state
        new_conn = DatabaseConnection(self.host, self.port)
        new_conn.is_connected = False
        return new_conn

    def __deepcopy__(self, memo):
        # Re-construct with freshly copied internal attributes
        new_conn = DatabaseConnection(
            copy.deepcopy(self.host, memo),
            copy.deepcopy(self.port, memo)
        )
        new_conn.is_connected = False
        memo[id(self)] = new_conn
        return new_conn

conn1 = DatabaseConnection("localhost", 5432)
conn2 = copy.copy(conn1)

print(conn1.is_connected)  # True
print(conn2.is_connected)  # False
```

---

## Python 3.13: `copy.replace()` (PEP 698)

Python 3.13 introduced `copy.replace()`, a standardized function to create a copy of an object with specified attributes replaced.

It operates on any object implementing the `__replace__()` method, including **dataclasses** and **namedtuples** out of the box:

```python
from dataclasses import dataclass
import copy

@dataclass(frozen=True)
class ServerConfig:
    host: str
    port: int
    debug: bool = False
    timeout: int = 30

# Original immutable configuration
prod_config = ServerConfig(host="api.production.internal", port=443, debug=False)

# Create a modified clone for local testing using copy.replace()
test_config = copy.replace(prod_config, host="127.0.0.1", port=8000, debug=True)

print("Original:", prod_config)
print("Modified:", test_config)
```

**Output:**
```text
Original: ServerConfig(host='api.production.internal', port=443, debug=False, timeout=30)
Modified: ServerConfig(host='127.0.0.1', port=8000, debug=True, timeout=30)
```

### Implementing `__replace__()` in Custom Classes
You can make your own classes compatible with `copy.replace()`:

```python
import copy

class RGBColor:
    def __init__(self, r: int, g: int, b: int):
        self.r = r
        self.g = g
        self.b = b

    def __replace__(self, **changes):
        new_values = {
            "r": self.r,
            "g": self.g,
            "b": self.b,
            **changes
        }
        return RGBColor(**new_values)

    def __repr__(self):
        return f"RGBColor({self.r}, {self.g}, {self.b})"

c1 = RGBColor(255, 0, 0)
c2 = copy.replace(c1, g=128)
print(c2)  # RGBColor(255, 128, 0)
```

---

## Summary Comparison

| Mechanism | Method | Nested Mutable Sharing | Use Case |
| :--- | :--- | :---: | :--- |
| **Assignment** | `b = a` | Shared | Renaming / passing references. |
| **Shallow Copy** | `copy.copy(a)` or `a.copy()` | Shared | Duplicate outer container only. |
| **Deep Copy** | `copy.deepcopy(a)` | Independent | Completely isolated copy of nested structures. |
| **Replacement Copy** | `copy.replace(a, ...)` (3.13+) | Custom | Immutable dataclasses and records with modified fields. |
