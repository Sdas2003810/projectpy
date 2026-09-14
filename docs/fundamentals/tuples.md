# Tuples

A tuple is an ordered, immutable sequence of items. Tuples are written with parentheses `(...)` with comma-separated elements.

Once created, items in a tuple cannot be added, removed, or modified.

---

## Creating Tuples

```python
# A tuple of integers
coordinates = (10, 20, 30)

# Parentheses are optional for tuple packing
point = 4, 5

# Empty tuple
empty = ()
```

### The Single-Element Tuple Rule

To define a tuple containing only one element, you **must include a trailing comma**:

<div class="example-box">
<div class="example-title">Example: Single Element Tuple</div>

```python
# Without comma: evaluated as integer inside parentheses
not_a_tuple = (50)
print(type(not_a_tuple))  # <class 'int'>

# With comma: recognized as a tuple
is_a_tuple = (50,)
print(type(is_a_tuple))   # <class 'tuple'>
```
</div>

---

## Differences Between Lists and Tuples

| Feature | List (`list`) | Tuple (`tuple`) |
| :--- | :--- | :--- |
| **Syntax** | Square brackets `[1, 2, 3]` | Parentheses `(1, 2, 3)` |
| **Mutability** | Mutable (can modify elements) | **Immutable** (cannot modify elements) |
| **Performance** | Larger memory footprint | Faster allocation and smaller memory size |
| **Dictionary Keys**| Cannot be used as dictionary keys | Can be used as keys (if items are hashable) |

---

## Tuple Packing and Unpacking

Tuple unpacking allows you to assign elements of a tuple directly to multiple variables:

<div class="example-box">
<div class="example-title">Example: Tuple Unpacking and Swapping</div>

```python
# Unpacking
person = ("Alice", 25, "Software Engineer")
name, age, profession = person

print("Name:", name)
print("Age:", age)
print("Profession:", profession)

# Variable swapping in Python
x = 10
y = 20
x, y = y, x
print("x:", x, "y:", y)
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
Name: Alice
Age: 25
Profession: Software Engineer
x: 20 y: 10
</div>
</div>

### Extended Unpacking with Asterisk (`*`)

Use the `*` prefix to catch remaining values into a list:

```python
first, *middle, last = [1, 2, 3, 4, 5]
print("First: ", first)   # 1
print("Middle:", middle)  # [2, 3, 4]
print("Last:  ", last)    # 5
```

---

## Named Tuples (`collections.namedtuple`)

The `collections.namedtuple` factory function assigns names to each position in a tuple, allowing attribute-style access:

<div class="example-box">
<div class="example-title">Example: Using namedtuple</div>

```python
from collections import namedtuple

Point = namedtuple("Point", ["x", "y"])
p = Point(11, 22)

print("x coordinate:", p.x)
print("y coordinate:", p.y)
print("By index:    ", p[0])
```
</div>
