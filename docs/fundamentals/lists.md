# Lists and Sequences

A list in Python is an ordered, mutable sequence of items. Lists are written as a list of comma-separated values between square brackets `[...]`. Items within a list can be of different data types.

---

## Creating Lists

```python
# A list of integers
numbers = [1, 2, 3, 4, 5]

# A list of strings
fruits = ["apple", "banana", "cherry"]

# A mixed data type list
mixed = ["Python", 3.14, 42, True]

# An empty list
empty_list = []
```

---

## Modifying Lists (Mutability)

Unlike strings, lists are mutable. You can change items directly:

<div class="example-box">
<div class="example-title">Example: Modifying List Elements</div>

```python
letters = ['a', 'b', 'c', 'd']

# Change a single item
letters[0] = 'A'
print(letters)  # ['A', 'b', 'c', 'd']

# Change a slice of items
letters[1:3] = ['B', 'C']
print(letters)  # ['A', 'B', 'C', 'd']

# Clear items by slice assignment
letters[2:] = []
print(letters)  # ['A', 'B']
```
</div>

---

## Common List Methods

| Method | Description | Example |
| :--- | :--- | :--- |
| `list.append(x)` | Adds an item to the end of the list | `nums.append(10)` |
| `list.extend(iterable)` | Appends all elements from another iterable | `nums.extend([20, 30])` |
| `list.insert(i, x)` | Inserts item `x` at given index `i` | `nums.insert(0, 5)` |
| `list.remove(x)` | Removes the first occurrence of item `x` | `nums.remove(10)` |
| `list.pop([i])` | Removes and returns item at index `i` (default last item) | `last = nums.pop()` |
| `list.clear()` | Removes all items from the list | `nums.clear()` |
| `list.index(x)` | Returns zero-based index of first occurrence of `x` | `idx = nums.index(20)` |
| `list.count(x)` | Returns number of times `x` appears in list | `cnt = nums.count(5)` |
| `list.sort()` | Sorts the items of the list in-place | `nums.sort()` |
| `list.reverse()` | Reverses the elements of the list in-place | `nums.reverse()` |
| `list.copy()` | Returns a shallow copy of the list | `copy_list = nums.copy()` |

<div class="example-box">
<div class="example-title">Example: List Methods in Practice</div>

```python
colors = ["red", "blue"]

colors.append("green")
print("After append:", colors)

colors.insert(1, "yellow")
print("After insert:", colors)

popped_color = colors.pop()
print("Popped:", popped_color)
print("After pop:", colors)
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
After append: ['red', 'blue', 'green']
After insert: ['red', 'yellow', 'blue', 'green']
Popped: green
After pop: ['red', 'yellow', 'blue']
</div>
</div>

---

## Assignment vs Copying (Reference Trap)

In Python, assigning a list variable to another variable creates a reference to the **same** underlying object in memory, not a new copy:

<div class="example-box">
<div class="example-title">Example: List References vs Shallow Copies</div>

```python
# 1. Assignment creates a reference to the same list:
a = [1, 2, 3]
b = a
b.append(4)
print("a:", a)  # a is modified too: [1, 2, 3, 4]

# 2. Creating an independent copy:
c = a.copy()    # or c = a[:]
c.append(99)
print("a:", a)  # [1, 2, 3, 4] (unchanged)
print("c:", c)  # [1, 2, 3, 4, 99]
```
</div>

---

## Built-in Functions with Lists

```python
values = [15, 3, 89, 42]

print("Length: ", len(values))   # 4
print("Minimum:", min(values))   # 3
print("Maximum:", max(values))   # 89
print("Sum:    ", sum(values))   # 149
```
