# Type Annotations Best Practices HOWTO

Type annotations (PEP 3107, PEP 484) allow developers to attach metadata to function parameters, return values, and class attributes. However, inspecting and evaluating annotations at runtime presents subtle gotchas.

---

## Accessing Annotations: Avoid Direct `__annotations__`

A common mistake is reading `obj.__annotations__` directly:

```python
# FRAGILE APPROACH (Discouraged):
class User:
    name: str

print(User.__annotations__)  # May raise AttributeError on classes without annotations!
```

Starting in **Python 3.10**, always use **`inspect.get_annotations()`**:

```python
import inspect

class Employee:
    name: str
    salary: float

# SAFEST APPROACH (Python 3.10+):
annotations = inspect.get_annotations(Employee)
print(annotations)
# {'name': <class 'str'>, 'salary': <class 'float'>}
```

`inspect.get_annotations()` handles class inheritance, forward references, and classes that have no annotations defined without raising `AttributeError`.

---

## The Forward Reference Problem & `from __future__ import annotations`

Consider a class method returning an instance of its own class:

```python
# Without future annotations, this raises NameError: name 'Node' is not defined!
# class Node:
#     def next_node(self) -> Node:
#         ...
```

Before Python 3.7, you had to wrap the type hint in quotes: `-> 'Node'`.

### Postponed Evaluation (PEP 563)

By adding `from __future__ import annotations` at the top of your file, Python avoids evaluating annotations at function definition time. Instead, it stores them as raw strings:

```python
from __future__ import annotations

class Node:
    def __init__(self, value: int, next_node: Node | None = None):
        self.value = value
        self.next = next_node

# Now valid and fully supported!
```

### Resolving Stringified Annotations

When runtime libraries (like Pydantic or FastAPI) need to convert stringified annotations back into real types, they pass `eval_str=True`:

```python
import inspect

annotations = inspect.get_annotations(Node.__init__, eval_str=True)
print(annotations["next_node"])  # Node | None (Evaluated class object)
```

---

## Python 3.14+: Deferred Evaluation (PEP 649 / 749)

In Python 3.14+, Python introduces **deferred evaluation** alongside the new `annotationlib` standard library module. Annotations are compiled as functions that are only executed when annotations are explicitly requested, resolving the tension between stringified hints and runtime reflection.
