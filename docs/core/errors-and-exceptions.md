# Errors and Exception Handling

Syntax errors and exceptions represent runtime conditions that disrupt normal code execution. Python provides structured exception handling mechanisms to catch, handle, and recover from errors cleanly.

---

## Syntax Errors vs Exceptions

- **Syntax Errors (Parsing Errors)**: The parser encounters invalid Python syntax before execution begins.
- **Exceptions**: Errors detected during execution (e.g. `ZeroDivisionError`, `TypeError`, `FileNotFoundError`).

---

## Handling Exceptions: `try` / `except` / `else` / `finally`

### Syntax Structure
```python
try:
    # Code that might raise an exception
except SpecificException as err:
    # Code to handle the exception
else:
    # Executes ONLY if no exception occurred in the try block
finally:
    # ALWAYS executes (for cleanup actions)
```

<div class="example-box">
<div class="example-title">Example: Complete Exception Handling</div>

```python
def safe_divide(a, b):
    try:
        result = a / b
    except ZeroDivisionError as err:
        print("Error: Cannot divide by zero.")
        return None
    except TypeError as err:
        print("Error: Operands must be numeric.")
        return None
    else:
        print("Division successful.")
        return result
    finally:
        print("Calculation attempt finished.")

print("Result 1:", safe_divide(10, 2))
print("Result 2:", safe_divide(10, 0))
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
Division successful.
Calculation attempt finished.
Result 1: 5.0
Error: Cannot divide by zero.
Calculation attempt finished.
Result 2: None
</div>
</div>

---

## Raising Exceptions (`raise`)

Use the `raise` statement to trigger an exception when conditions are invalid:

```python
def set_age(age: int):
    if age < 0:
        raise ValueError("Age cannot be negative.")
    print(f"Age set to {age}")
```

---

## Exception Chaining (`from`)

When re-raising an exception to provide higher-level context, you can preserve the original cause using `from`:

```python
try:
    with open("database.conf") as f:
        config = f.read()
except FileNotFoundError as err:
    raise RuntimeError("Application configuration missing") from err
```

---

## Custom Exception Classes

User-defined exceptions should inherit from Python's built-in `Exception` class:

<div class="example-box">
<div class="example-title">Example: Defining Custom Exception</div>

```python
class InsufficientFundsError(Exception):
    """Exception raised when an account withdrawal exceeds balance."""
    def __init__(self, balance, amount):
        super().__init__(f"Attempted to withdraw ${amount} with balance ${balance}")
        self.balance = balance
        self.amount = amount

def withdraw(balance, amount):
    if amount > balance:
        raise InsufficientFundsError(balance, amount)
    return balance - amount
```
</div>

---

## Exception Groups and `except*` (Python 3.11+)

Python 3.11 introduced `ExceptionGroup`, which allows multiple independent exceptions to be raised and handled simultaneously (especially useful in concurrent tasks):

```python
try:
    raise ExceptionGroup("Multiple errors occurred", [
        ValueError("Invalid value"),
        TypeError("Invalid type"),
        KeyError("Missing key")
    ])
except* ValueError as eg:
    print("Handled ValueErrors:", eg.exceptions)
except* (TypeError, KeyError) as eg:
    print("Handled Type and Key Errors:", eg.exceptions)
```

---

## Adding Notes to Exceptions (Python 3.11+)

Exceptions have an `.add_note(message)` method to attach context without modifying the error message itself:

```python
try:
    raise ValueError("Invalid configuration setting")
except ValueError as err:
    err.add_note("Check your config.ini file line 42")
    raise
```
