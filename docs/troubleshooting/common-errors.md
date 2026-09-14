# Common Python Errors and Solutions

When Python encounters a situation it cannot execute, it terminates the script and prints a **Traceback**. Understanding how to interpret tracebacks makes debugging straightforward.

---

## How to Read a Python Traceback

A Python traceback should be read from the **bottom to the top**:

```text
Traceback (most recent call last):
  File "calculator.py", line 18, in <module>
    result = compute(user_input)
  File "calculator.py", line 8, in compute
    return 100 / value
ZeroDivisionError: division by zero
```

1. **Bottom line**: States the exact exception type (`ZeroDivisionError`) and the descriptive error message (`division by zero`).
2. **Line above the bottom**: Indicates the exact file name (`calculator.py`) and line number (`line 8`) where the exception occurred.

---

## 1. SyntaxError: Invalid Syntax

### Cause
Python encountered code that violates language grammar rules before execution began.

<div class="example-box">
<div class="example-title">Example: Missing Colon or Parenthesis</div>

```python
# Incorrect: Missing colon after if statement
# if score > 50
#     print("Pass")

# Correct:
if score > 50:
    print("Pass")
```
</div>

---

## 2. IndentationError: Expected an Indented Block

### Cause
Python expects an indented block after statements ending in a colon (`:`) such as `def`, `if`, `for`, `while`, or `class`.

<div class="example-box">
<div class="example-title">Example: Missing Indentation</div>

```python
# Incorrect:
# def greet():
# print("Hello")

# Correct:
def greet():
    print("Hello")
```
</div>

---

## 3. NameError: Name is Not Defined

### Cause
A variable or function is referenced that has not been defined in the current scope, or its name was misspelled.

<div class="example-box">
<div class="example-title">Example: Typo in Variable Name</div>

```python
user_name = "Alice"

# Incorrect (spelling mistake):
# print(username)  # NameError: name 'username' is not defined

# Correct:
print(user_name)
```
</div>

---

## 4. TypeError: Incompatible Types or Arguments

### Cause
An operation or function was applied to an object of an inappropriate type.

<div class="example-box">
<div class="example-title">Example: Concatenating String and Integer</div>

```python
age = 25

# Incorrect:
# message = "Age: " + age  # TypeError: can only concatenate str to str

# Correct: Use f-strings or str()
message = f"Age: {age}"
```
</div>

---

## 5. IndexError: List Index Out of Range

### Cause
An index position was requested that is greater than or equal to the length of the sequence.

<div class="example-box">
<div class="example-title">Example: Out-of-bounds Index</div>

```python
items = ["apple", "banana"]

# Incorrect: Indices are 0 and 1
# print(items[2])  # IndexError: list index out of range

# Correct:
if len(items) > 2:
    print(items[2])
```
</div>

---

## 6. KeyError: Key Not Found

### Cause
A dictionary key was accessed using square brackets `dict[key]` that does not exist.

<div class="example-box">
<div class="example-title">Example: Missing Dictionary Key</div>

```python
user = {"id": 1, "username": "alice"}

# Incorrect:
# email = user["email"]  # KeyError: 'email'

# Correct: Use .get() with a default value
email = user.get("email", "Not Provided")
print(email)
```
</div>

---

## 7. AttributeError: Object Has No Attribute

### Cause
A method or attribute was accessed that does not exist on that data type.

<div class="example-box">
<div class="example-title">Example: Calling Non-Existent Method</div>

```python
text = "hello"

# Incorrect: Strings do not have an append() method
# text.append(" world") # AttributeError: 'str' object has no attribute 'append'

# Correct: Use concatenation (+)
text = text + " world"
```
</div>
