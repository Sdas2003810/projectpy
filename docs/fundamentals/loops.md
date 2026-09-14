# Loops (`for` and `while`)

Loops are used to execute a block of code repeatedly. Python provides two basic loop statements: `for` and `while`.

---

## 1. The `for` Loop

In Python, the `for` statement iterates over the items of any sequence (such as a list, tuple, or string) in the order that they appear.

### Syntax
```python
for item in sequence:
    # statements
```

<div class="example-box">
<div class="example-title">Example: Iterating Over a List</div>

```python
languages = ["Python", "Java", "C++"]
for lang in languages:
    print(lang)
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
Python
Java
C++
</div>
</div>

---

## The `range()` Function

To iterate over a sequence of numbers, use the built-in `range()` function:

### Syntax
```python
range(stop)
range(start, stop[, step])
```

- `start`: Initial number (default 0).
- `stop`: Upper limit (exclusive; the sequence stops before this number).
- `step`: Increment value (default 1).

<div class="example-box">
<div class="example-title">Example: Using range()</div>

```python
# 0 to 4
for i in range(5):
    print(i, end=" ")
print()

# 2 to 10 with step 2
for n in range(2, 11, 2):
    print(n, end=" ")
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
0 1 2 3 4
2 4 6 8 10
</div>
</div>

---

## Looping Helper Functions: `enumerate()` and `zip()`

### 1. `enumerate()`: Index and Value Together

When looping through a sequence and tracking index position, use `enumerate()`:

```python
frameworks = ["Django", "FastAPI", "Flask"]
for index, item in enumerate(frameworks, start=1):
    print(f"{index}: {item}")
```

### 2. `zip()`: Multiple Sequences in Parallel

```python
names = ["Alice", "Bob", "Charlie"]
scores = [90, 85, 95]

for name, score in zip(names, scores):
    print(f"{name} scored {score}")
```

---

## 2. The `while` Loop

A `while` loop executes as long as a specified condition remains `True`.

### Syntax
```python
while condition:
    # statements
```

<div class="example-box">
<div class="example-title">Example: Fibonacci Series using while</div>

```python
# Fibonacci series: the sum of two preceding numbers
a, b = 0, 1
while a < 50:
    print(a, end=" ")
    a, b = b, a + b
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
0 1 1 2 3 5 8 13 21 34
</div>
</div>

---

## Loop Control Statements: `break`, `continue`, and `pass`

- `break`: Terminates the current loop execution immediately.
- `continue`: Skips the remainder of the current iteration and advances to the next iteration.
- `pass`: Does nothing. It is used as a syntactic placeholder.

<div class="example-box">
<div class="example-title">Example: break and continue</div>

```python
# break example
for n in range(1, 10):
    if n == 5:
        break
    print(n, end=" ")  # 1 2 3 4
print()

# continue example
for n in range(1, 6):
    if n == 3:
        continue  # Skip 3
    print(n, end=" ")  # 1 2 4 5
```
</div>

---

## The `else` Clause on Loops

In Python, loops can have an `else` clause. The `else` block executes **only if the loop completes without encountering a `break` statement**:

<div class="example-box">
<div class="example-title">Example: Finding Prime Numbers with loop else</div>

```python
for n in range(2, 10):
    for x in range(2, n):
        if n % x == 0:
            print(f"{n} equals {x} * {n // x}")
            break
    else:
        # Loop fell through without finding a factor
        print(f"{n} is a prime number")
```
</div>
