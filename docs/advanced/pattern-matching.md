# Structural Pattern Matching

Introduced in **Python 3.10 (PEP 634, 635, 636)**, structural pattern matching is one of the most powerful additions to the Python language.

While developers familiar with C, Java, or JavaScript might initially mistake `match/case` for a standard `switch` statement, it is vastly more expressive. It inspects the **structure** and **types** of complex data objects, extracts their components into variables (**destructuring**), and conditionally executes code paths based on matching criteria.

---

## Basic Syntax and Literal Patterns

The fundamental form pairs a `match` expression with one or more `case` branches:

```python
def handle_http_status(status: int) -> str:
    match status:
        case 200:
            return "OK"
        case 400:
            return "Bad Request"
        case 404:
            return "Not Found"
        case 500:
            return "Internal Server Error"
        case _:
            # Wildcard: matches anything (default fallback)
            return f"Unhandled HTTP status: {status}"
```

### The Wildcard Pattern (`_`)

The underscore `_` acts as a wildcard pattern. It matches any value but **does not bind** the value to a variable name. It must always be the final case branch.

---

## Combining Patterns with OR (`|`)

Use the pipe `|` operator to match any of several patterns:

```python
def check_access(role: str) -> str:
    match role.lower():
        case "admin" | "root" | "superuser":
            return "Full system access granted."
        case "editor" | "contributor":
            return "Edit access granted."
        case "viewer" | "guest":
            return "Read-only access granted."
        case _:
            return "Access denied."
```

---

## Sequence Patterns and Destructuring

Sequence patterns match lists, tuples, or custom sequences, destructuring elements into bound variables:

```python
def process_command(command: list[str]):
    match command:
        case ["quit"]:
            print("Shutting down engine...")
        case ["go", direction]:
            print(f"Moving character towards {direction}...")
        case ["attack", target, weapon]:
            print(f"Attacking {target} with {weapon}!")
        case ["teleport", int(x), int(y)]:
            print(f"Teleporting to coordinates ({x}, {y}).")
        case _:
            print(f"Unknown command syntax: {command}")

process_command(["go", "north"])
process_command(["attack", "dragon", "frost_axe"])
```

<div class="terminal-output">
Moving character towards north...
Attacking dragon with frost_axe!
</div>

### Starred Unpacking in Sequences

You can capture remaining items using `*rest`:

```python
def analyze_scores(scores: list[int]):
    match scores:
        case []:
            print("No scores submitted.")
        case [single]:
            print(f"Single score submitted: {single}")
        case [first, *middle, last]:
            print(f"First: {first}, Last: {last}, Intermediate count: {len(middle)}")

analyze_scores([95, 88, 76, 82, 91])
# First: 95, Last: 91, Intermediate count: 3
```

!!! note "Strings and Bytes are Not Sequences for Pattern Matching"
    Although strings and bytes are sequences in Python, `match/case` deliberately treats them as single atomic literals rather than sequences of characters, preventing accidental character-by-character destructuring.

---

## Mapping Patterns (Dictionaries)

Mapping patterns match dictionary keys and bind their values. Unlike sequences, mapping patterns perform **partial matching** by default: extra unmentioned keys are ignored.

```python
def handle_event(event: dict):
    match event:
        case {"type": "click", "x": x, "y": y}:
            print(f"Mouse click recorded at ({x}, {y})")
        case {"type": "keypress", "key": key, "modifiers": mods}:
            print(f"Key '{key}' pressed with modifiers {mods}")
        case {"type": "notification", "message": msg, **extra}:
            print(f"Notification: '{msg}'. Ignored extra fields: {list(extra.keys())}")
        case _:
            print("Unrecognized event structure.")

handle_event({"type": "click", "x": 120, "y": 450, "timestamp": 169871234})
# Output: Mouse click recorded at (120, 450)
```

---

## Class Patterns

Structural pattern matching can match against class instances and destructure their attributes:

```python
from dataclasses import dataclass

@dataclass
class Point:
    x: float
    y: float

@dataclass
class Circle:
    center: Point
    radius: float

def describe_shape(shape):
    match shape:
        case Point(x=0, y=0):
            print("Point at origin (0, 0)")
        case Point(x=x, y=0):
            print(f"Point on X-axis at x={x}")
        case Point(x=x, y=y):
            print(f"Point at ({x}, {y})")
        case Circle(center=Point(x=0, y=0), radius=r):
            print(f"Circle centered at origin with radius {r}")
        case Circle(center=Point(x=x, y=y), radius=r):
            print(f"Circle at ({x}, {y}) with radius {r}")
        case _:
            print("Unknown geometric shape.")

describe_shape(Circle(center=Point(0, 0), radius=5.0))
```

<div class="terminal-output">
Circle centered at origin with radius 5.0
</div>

### Custom Classes and `__match_args__`

Dataclasses automatically generate `__match_args__` for positional matching. For standard custom classes, define `__match_args__` manually:

```python
class Vector3D:
    __match_args__ = ("x", "y", "z")

    def __init__(self, x, y, z):
        self.x = x
        self.y = y
        self.z = z

# Positional class pattern matching now works:
def inspect_vector(vec):
    match vec:
        case Vector3D(0, 0, 0):
            print("Zero vector")
        case Vector3D(x, y, 0):
            print(f"2D vector in XY plane: ({x}, {y})")
        case Vector3D(x, y, z):
            print(f"3D vector: ({x}, {y}, {z})")
```

---

## Guard Clauses (`if` Expressions)

You can append an `if` condition to any `case` pattern. The case only matches if both the structural pattern matches AND the guard condition evaluates to `True`:

```python
def check_order(order: dict):
    match order:
        case {"items": items, "total": total} if total > 1000 and len(items) > 10:
            print("Bulk enterprise order: apply 15% discount.")
        case {"items": items, "total": total} if total > 500:
            print("Standard volume order: apply 5% discount.")
        case {"items": items, "total": total}:
            print("Standard order: no discount.")
        case _:
            print("Invalid order structure.")
```

---

## The `as` Pattern

The `as` keyword binds an entire sub-pattern to a name while simultaneously destructuring its inner components:

```python
def inspect_nested(data):
    match data:
        case [Point(x, y) as pt1, Point(x2, y2) as pt2]:
            print(f"Line segment from {pt1} to {pt2}")
```

---

## Comparison: `match/case` vs `if/elif/else`

| Feature | `if/elif/else` | `match/case` |
| :--- | :--- | :--- |
| **Primary purpose** | Boolean condition evaluation | Structural validation & destructuring |
| **Type matching** | Requires manual `isinstance()` checks | Built-in via class patterns |
| **Dictionary extraction** | Verbose `.get()` with key checks | Declarative shape matching |
| **Sequence unpacking** | Manual length checks & indexing | Declarative pattern matching |
| **Performance** | Evaluated sequentially | Highly optimized jump/dispatch bytecode |
