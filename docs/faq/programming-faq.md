# Programming FAQ

Detailed solutions to the most common programming dilemmas, misconceptions, and subtle edge cases in Python.

---

## The Default Mutable Argument Trap

### Question
Why does my function keep accumulating items from previous calls when I use `def func(items=[])`?

### Answer
Default parameter expressions are evaluated **once**, at the exact moment the function is defined, **not** each time the function is called.
If the default is a mutable object (like a `list` or `dict`), all subsequent calls share the exact same instance in memory!

=== "Wrong (Shares Mutable State)"
    ```python
    def add_item(val, target=[]):
        target.append(val)
        return target

    print(add_item(1))  # [1]
    print(add_item(2))  # [1, 2] -- BUG! Accumulates across calls!
    ```

=== "Correct Idiom"
    ```python
    def add_item(val, target=None):
        if target is None:
            target = []
        target.append(val)
        return target

    print(add_item(1))  # [1]
    print(add_item(2))  # [2] -- Works cleanly!
    ```

---

## Creating Multi-Dimensional Lists

### Question
Why does updating one element in my 2D matrix change an entire column?

### Answer
Writing `[[0] * 3] * 3` creates an outer list containing three references to the **exact same inner list**:

```python
# BUGGY INITIALIZATION:
grid = [[0] * 3] * 3
grid[0][0] = 99
print(grid)  # [[99, 0, 0], [99, 0, 0], [99, 0, 0]] -- All 3 rows changed!
```

### Solution: List Comprehension
Always initialize 2D grids using list comprehensions so each row is an independent instance:

```python
# CORRECT: Creates fresh, independent rows
grid = [[0 for _ in range(3)] for _ in range(3)]
grid[0][0] = 99
print(grid)  # [[99, 0, 0], [0, 0, 0], [0, 0, 0]]
```

---

## Modifying a List While Iterating Over It

### Question
Why does my loop skip items when I call `list.remove()` during iteration?

### Answer
When you delete an item, the remaining elements shift left by one index. The internal loop iterator advances its index by 1, stepping directly over the newly shifted element:

```python
# BUGGY:
numbers = [1, 2, 2, 3, 4]
for x in numbers:
    if x == 2:
        numbers.remove(x)

print(numbers)  # [1, 2, 3, 4] -- Second '2' was skipped!
```

### Solutions

1. **Use a List Comprehension (Fastest & cleanest)**:
    ```python
    numbers = [x for x in numbers if x != 2]
    ```
2. **Iterate over a copy slice**:
    ```python
    for x in numbers[:]:  # Slicing creates a snapshot copy
        if x == 2:
            numbers.remove(x)
    ```

---

## Is Python Call-by-Value or Call-by-Reference?

### Answer
Neither! Python uses **Call-by-Object-Reference** (also called **Pass-by-Assignment**).

- When you pass an argument to a function, the parameter is bound to the same object.
- If you **reassign** the parameter name (`x = 10`), it points to a new object; the caller's variable remains unchanged.
- If you **mutate** a mutable object in-place (`x.append(1)`), the change is visible to the caller!

```python
def modify(lst, num):
    lst.append("mutated")  # Mutates the caller's list!
    num = 999              # Rebinds local variable; caller unaffected

my_list = [1, 2]
my_num = 10
modify(my_list, my_num)

print(my_list)  # [1, 2, 'mutated']
print(my_num)   # 10
```

---

## How do I copy an object in Python?

Assignment (`b = a`) only copies the reference, not the underlying object. Use the `copy` module:

```python
import copy

original = {"users": ["Alice", "Bob"]}

# Shallow Copy: creates new outer dict, but inner list is still shared!
shallow = copy.copy(original)

# Deep Copy: recursively copies all nested objects
deep = copy.deepcopy(original)

original["users"].append("Charlie")
print("Shallow copy:", shallow["users"])  # ['Alice', 'Bob', 'Charlie']
print("Deep copy:   ", deep["users"])     # ['Alice', 'Bob']
```

---

## The Tuple Augmented Assignment Gotcha

### Question
Why does `t[0] += [3]` raise a `TypeError` but still modify the list inside the tuple?

```python
t = ([1, 2], "hello")
try:
    t[0] += [3]
except TypeError as e:
    print("Error raised:", e)

print(t)  # ([1, 2, 3], 'hello') -- It actually added the 3!
```

### Answer
The `+=` operator executes in two steps:
1. It calls `t[0].extend([3])` on the inner list (which succeeds and mutates the list in-place).
2. It then attempts assignment: `t[0] = mutated_list`. Because tuples are immutable, the assignment step fails with a `TypeError`.
The in-place mutation succeeded, but the subsequent tuple assignment failed!
