# Control Flow (if, elif, else)

Python uses standard control flow statements (`if`, `elif`, `else`) to make decisions based on boolean conditions.

---

## Python Indentation Rule

Unlike C, Java, or JavaScript, which use curly braces `{}` to delimit blocks of code, **Python uses indentation (whitespace)**.

- PEP 8 standard recommendation is **4 spaces per indentation level**.
- Do not mix tabs and spaces.
- Statements that start a block (`if`, `for`, `def`, `class`) always end with a colon `:`.

<div class="example-box">
<div class="example-title">Example: Indentation in Python</div>

```python
score = 85

if score >= 50:
    # Indented 4 spaces: inside the if block
    print("Exam Passed")
    print("Congratulations!")

print("This line always executes because it is unindented.")
```
</div>

---

## The `if`, `elif`, and `else` Statements

### Syntax
```python
if condition1:
    # statement 1
elif condition2:
    # statement 2
else:
    # fallback statement
```

<div class="example-box">
<div class="example-title">Example: Letter Grading System</div>

```python
marks = 78

if marks >= 90:
    grade = "A"
elif marks >= 80:
    grade = "B"
elif marks >= 70:
    grade = "C"
else:
    grade = "F"

print("Assigned Grade:", grade)
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
Assigned Grade: C
</div>
</div>

---

## Comparison: Value Equality (`==`) vs Identity (`is`)

- `==` compares the **values** of two objects.
- `is` checks whether two variables point to the **exact same memory location** (`id(a) == id(b)`).

```python
list1 = [1, 2, 3]
list2 = [1, 2, 3]

print(list1 == list2)  # True  (same values)
print(list1 is list2)  # False (different objects in memory)

# Comparing with None: Always use 'is' or 'is not'
value = None
if value is None:
    print("Value is None")
```

---

## Truth Value Testing (Truthy and Falsy)

In Python, any object can be tested for truth value:

### Falsy Values
The following objects evaluate to `False` in conditions:
- Constants: `None`, `False`
- Numeric zeros: `0`, `0.0`, `0j`
- Empty sequences and collections: `""`, `()`, `[]`, `{}`, `set()`

All other objects evaluate to `True` (Truthy).

<div class="example-box">
<div class="example-title">Example: Checking Truthiness</div>

```python
items = []

# Idiomatic check for empty list
if not items:
    print("The list is empty.")
```
</div>

---

## Conditional Expression (Ternary Operator)

Assign a value conditionally in a single readable line:

### Syntax
```python
value_if_true if condition else value_if_false
```

```python
age = 20
status = "Adult" if age >= 18 else "Minor"
print(status)  # Adult
```

---

## Pattern Matching (`match` / `case` in Python 3.10+)

Python 3.10 introduced structural pattern matching:

<div class="example-box">
<div class="example-title">Example: Structural Pattern Matching</div>

```python
def check_status(code: int) -> str:
    match code:
        case 200:
            return "OK"
        case 301 | 302:
            return "Redirect"
        case 404:
            return "Not Found"
        case 500:
            return "Server Error"
        case _:
            return "Unknown Code"

print(check_status(404))  # Not Found
```
</div>
