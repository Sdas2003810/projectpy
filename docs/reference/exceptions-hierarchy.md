# Built-in Exceptions Hierarchy

All built-in exceptions in Python inherit from `BaseException`. Understanding the class hierarchy is crucial for writing correct error-handling logic with `try...except`.

---

## The Complete Built-in Exception Tree

```
BaseException
 ├── BaseExceptionGroup (Python 3.11+)
 ├── GeneratorExit
 ├── KeyboardInterrupt
 ├── SystemExit
 └── Exception
      ├── ArithmeticError
      │    ├── FloatingPointError
      │    ├── OverflowError
      │    └── ZeroDivisionError
      ├── AssertionError
      ├── AttributeError
      ├── BufferError
      ├── EOFError
      ├── ExceptionGroup [subclass of Exception and BaseExceptionGroup] (Python 3.11+)
      ├── ImportError
      │    └── ModuleNotFoundError
      ├── LookupError
      │    ├── IndexError
      │    └── KeyError
      ├── MemoryError
      ├── NameError
      │    └── UnboundLocalError
      ├── OSError
      │    ├── BlockingIOError
      │    ├── ChildProcessError
      │    ├── ConnectionError
      │    │    ├── BrokenPipeError
      │    │    ├── ConnectionAbortedError
      │    │    ├── ConnectionRefusedError
      │    │    └── ConnectionResetError
      │    ├── FileExistsError
      │    ├── FileNotFoundError
      │    ├── InterruptedError
      │    ├── IsADirectoryError
      │    ├── NotADirectoryError
      │    ├── PermissionError
      │    ├── ProcessLookupError
      │    └── TimeoutError
      ├── ReferenceError
      ├── RuntimeError
      │    ├── NotImplementedError
      │    └── RecursionError
      ├── StopAsyncIteration
      ├── StopIteration
      ├── SyntaxError
      │    └── IndentationError
      │         └── TabError
      ├── SystemError
      ├── TypeError
      ├── ValueError
      │    └── UnicodeError
      │         ├── UnicodeDecodeError
      │         ├── UnicodeEncodeError
      │         └── UnicodeTranslateError
      └── Warning
           ├── BytesWarning
           ├── DeprecationWarning
           ├── EncodingWarning (Python 3.10+)
           ├── FutureWarning
           ├── ImportWarning
           ├── PendingDeprecationWarning
           ├── ResourceWarning
           ├── RuntimeWarning
           ├── SyntaxWarning
           ├── UnicodeWarning
           └── UserWarning
```

---

## `BaseException` vs `Exception`

!!! danger "Never catch `BaseException` blindly!"
    Catching `BaseException` intercepts system control signals:
    - **`KeyboardInterrupt`**: Raised when the user presses `Ctrl+C`. Catching it prevents users from stopping your program!
    - **`SystemExit`**: Raised by `sys.exit()`. Catching it prevents your program from cleanly terminating.
    - **`GeneratorExit`**: Raised when a generator's `.close()` method is called.
    
    Always catch **`Exception`** for application-level errors:
    
    ```python
    # CORRECT:
    try:
        process_task()
    except Exception as e:
        logger.error(f"Task failed: {e}")
    ```

---

## Grouping Errors by Base Class

Catching a base exception handles all of its derived subclasses:

```python
def retrieve_value(collection, key_or_index):
    try:
        return collection[key_or_index]
    except LookupError as e:
        # Catches BOTH IndexError (for lists) AND KeyError (for dicts)!
        print(f"Lookup failed: {e}")
```

---

## Modern Exception Handling Features

### 1. Exception Notes with `add_note()` (Python 3.11+)

Add diagnostic context without modifying the exception message or type:

```python
try:
    raise ValueError("Invalid configuration setting")
except ValueError as err:
    err.add_note("Config source: /etc/app/production.toml")
    err.add_note("Failed while parsing [database.pool_size]")
    raise
```

### 2. Exception Groups & `except*` (Python 3.11+, PEP 654)

Handles multiple concurrent exceptions (such as those raised concurrently in `asyncio.TaskGroup`):

```python
try:
    raise ExceptionGroup(
        "Multiple background errors",
        [ValueError("Invalid payload"), FileNotFoundError("Missing cache")]
    )
except* ValueError as eg:
    print(f"Handled ValueErrors: {eg.exceptions}")
except* FileNotFoundError as eg:
    print(f"Handled FileErrors: {eg.exceptions}")
```

### 3. Explicit Exception Chaining (`raise ... from`)

```python
try:
    db.connect()
except ConnectionRefusedError as orig:
    # Retain original cause in traceback:
    raise DatabaseError("Could not reach replica") from orig

    # Or suppress the context explicitly:
    # raise DatabaseError("Could not reach replica") from None
```
