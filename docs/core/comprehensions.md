# List, Dict, and Set Comprehensions

Comprehensions provide a concise way to create new sequences (such as lists, dictionaries, and sets) from existing collections without writing verbose `for` loops and `.append()` calls.

---

## 1. List Comprehensions

A list comprehension consists of brackets containing an expression followed by a `for` clause, then zero or more `for` or `if` clauses.

### Syntax
```python
[expression for item in iterable if condition]
```

<div class="example-box">
<div class="example-title">Example: Traditional Loop vs List Comprehension</div>

```python
# Traditional loop:
squares = []
for x in range(1, 6):
    squares.append(x ** 2)

# Equivalent list comprehension:
comp_squares = [x ** 2 for x in range(1, 6)]

print(comp_squares)
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
[1, 4, 9, 16, 25]
</div>
</div>

### Filtering with `if` Clause

```python
numbers = [12, 5, 8, 19, 22, 7, 30]
evens = [n for n in numbers if n % 2 == 0]
print(evens)  # [12, 8, 22, 30]
```

### Nested List Comprehensions

Flatten a 2-dimensional matrix into a single list:

```python
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

flattened = [val for row in matrix for val in row]
print(flattened)  # [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

---

## 2. Dictionary Comprehensions

Dictionary comprehensions construct dictionaries from iterables:

### Syntax
```python
{key_expression: value_expression for item in iterable if condition}
```

<div class="example-box">
<div class="example-title">Example: Creating and Inverting Dictionaries</div>

```python
# Number to cube mapping
cubes = {n: n ** 3 for n in range(1, 6)}
print("Cubes:", cubes)

# Inverting a dictionary (swapping keys and values)
original = {"a": 1, "b": 2, "c": 3}
inverted = {v: k for k, v in original.items()}
print("Inverted:", inverted)
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
Cubes: {1: 1, 2: 8, 3: 27, 4: 64, 5: 125}
Inverted: {1: 'a', 2: 'b', 3: 'c'}
</div>
</div>

---

## 3. Set Comprehensions

Set comprehensions create sets, automatically deduplicating values:

### Syntax
```python
{expression for item in iterable if condition}
```

```python
words = ["apple", "banana", "avocado", "cherry", "apricot"]
first_letters = {word[0].upper() for word in words}
print(first_letters)  # {'A', 'B', 'C'}
```

---

## 4. Generator Expressions

If you are dealing with large datasets and do not need the entire list loaded into memory at once, use a **generator expression** by replacing square brackets `[...]` with parentheses `(...)`:

```python
# Generator expression (lazy evaluation)
sum_of_squares = sum(x ** 2 for x in range(1_000_000))
print("Sum:", sum_of_squares)
```
