# Context Managers and the `with` Statement

Whenever you open a file, acquire a thread lock, or start a database transaction, you enter into a contract: whatever resources you acquire, you must eventually release. If your program crashes halfway through or raises an unhandled exception, unreleased file descriptors leak, database connections stay open, and deadlocks occur.

Context managers solve this cleanly by binding resource allocation and teardown to a lexical code block via the `with` statement.

---

## The `with` Statement Syntax

Before context managers were introduced in Python 2.5 (PEP 343), safe cleanup required verbose `try...finally` blocks:

=== "With Context Manager"
    ```python
    with open("server.log", "r", encoding="utf-8") as file:
        data = file.read()
    # file is guaranteed to be closed here, even if read() raised an error
    ```

=== "Without Context Manager (Manual)"
    ```python
    file = open("server.log", "r", encoding="utf-8")
    try:
        data = file.read()
    finally:
        file.close()
    ```

The `with` statement is not syntactic sugar for just closing files; it is a general protocol for managing setup and teardown actions around any block of code.

---

## The Context Management Protocol

An object becomes a context manager by implementing two special dunder methods:

1. `__enter__(self)`: Prepares the resource. The return value of this method is bound to the variable after the `as` keyword.
2. `__exit__(self, exc_type, exc_val, exc_tb)`: Executes teardown logic when execution leaves the `with` block.

```
       +-------------------------+
       |   Enter `with` block    |
       +------------+------------+
                    |
                    v
       +-------------------------+
       | mgr.__enter__() called  | ---> Return value assigned to `as target`
       +------------+------------+
                    |
                    v
       +-------------------------+
       | Execute inner code body |
       +------------+------------+
                    |
         +----------+----------+
         |                     |
     No error             Exception raised
         |                     |
         v                     v
+------------------+   +------------------------------------+
| __exit__(None,   |   | __exit__(exc_type, exc_val, exc_tb)|
|          None,   |   +-----------------+------------------+
|          None)   |                     |
+--------+---------+          Returns True?
         |                     /       \
         |                   Yes        No
         |                   /            \
         |         Exception suppressed   Exception re-raised
         \                 /               /
          v               v               v
       +------------------------------------+
       | Continue after `with` block or die |
       +------------------------------------+
```

### Implementing a Custom Context Manager

Here is an example of a performance timer context manager:

```python
import time
from typing import Optional, Type
from types import TracebackType

class Timer:
    def __init__(self, label: str = "Execution"):
        self.label = label
        self.start_time: float = 0.0
        self.elapsed: float = 0.0

    def __enter__(self) -> "Timer":
        self.start_time = time.perf_counter()
        return self  # Bound to the target in `as <var>`

    def __exit__(
        self,
        exc_type: Optional[Type[BaseException]],
        exc_val: Optional[BaseException],
        exc_tb: Optional[TracebackType]
    ) -> bool:
        self.elapsed = time.perf_counter() - self.start_time
        print(f"[{self.label}] Elapsed time: {self.elapsed:.6f} seconds")
        
        # Returning False (or None) allows any raised exception to propagate.
        # Returning True suppresses the exception.
        return False
```

Using the timer:

```python
with Timer("Data processing") as timer:
    total = sum(i ** 2 for i in range(1_000_000))

print(f"Recorded elapsed: {timer.elapsed:.4f}s")
```

<div class="terminal-output">
[Data processing] Elapsed time: 0.084123 seconds
Recorded elapsed: 0.0841s
</div>

---

## Handling Exceptions in `__exit__`

The three arguments passed to `__exit__` convey exception details if an error occurred inside the `with` block:

| Parameter | Value when no exception occurs | Value when exception is raised |
| :--- | :--- | :--- |
| `exc_type` | `None` | The exception class (e.g., `ValueError`) |
| `exc_val` | `None` | The exception instance (e.g., `ValueError("invalid value")`) |
| `exc_tb` | `None` | A traceback object |

### Suppressing Exceptions

If `__exit__()` returns `True`, Python suppresses the exception and execution resumes immediately after the `with` block.

```python
class SuppressErrors:
    def __init__(self, *exceptions_to_ignore: Type[BaseException]):
        self.exceptions = exceptions_to_ignore

    def __enter__(self):
        return self

    def __exit__(self, exc_type, exc_val, exc_tb) -> bool:
        if exc_type is not None and issubclass(exc_type, self.exceptions):
            print(f"Suppressed expected exception: {exc_val}")
            return True  # Suppress!
        return False  # Let unexpected exceptions bubble up

# Example usage:
with SuppressErrors(FileNotFoundError, KeyError):
    data = {"name": "Alice"}
    print(data["age"])  # KeyError suppressed

print("Program continued running smoothly!")
```

<div class="terminal-output">
Suppressed expected exception: 'age'
Program continued running smoothly!
</div>

!!! warning "Be Cautious When Suppressing Exceptions"
    Never blindly return `True` from `__exit__` unless you have explicit logic verifying that the error is benign. Suppressing unexpected bugs (like `NameError`, `TypeError`, or `KeyboardInterrupt`) makes debugging nearly impossible.

---

## Multiple Context Managers

You can enter multiple context managers in a single `with` statement by separating them with commas.

Starting in **Python 3.10 (PEP 617)**, you can enclose multiple context managers in parentheses, allowing clean multi-line formatting without trailing backslashes:

=== "Python 3.10+ (Parenthesized Syntax)"
    ```python
    with (
        open("source.txt", "r", encoding="utf-8") as src,
        open("destination.txt", "w", encoding="utf-8") as dst,
    ):
        content = src.read()
        dst.write(content.upper())
    ```

=== "Python 3.9 and Earlier"
    ```python
    with open("source.txt", "r", encoding="utf-8") as src, \
         open("destination.txt", "w", encoding="utf-8") as dst:
        content = src.read()
        dst.write(content.upper())
    ```

When managing multiple contexts, they behave as nested blocks:
- `src.__enter__()` runs first, followed by `dst.__enter__()`.
- Teardown occurs in reverse order: `dst.__exit__()` runs first, then `src.__exit__()`.

---

## Context Managers Using `@contextlib.contextmanager`

Writing an entire class with `__enter__` and `__exit__` can be overkill for simple tasks. The standard library provides the `contextlib` module, which turns a generator function into a context manager using a single `yield` statement.

```python
from contextlib import contextmanager
import os

@contextmanager
def temporary_working_directory(target_dir: str):
    original_dir = os.getcwd()
    try:
        os.chdir(target_dir)
        yield target_dir  # Execution yields control to the `with` block
    finally:
        # Guaranteed cleanup when exiting `with` block
        os.chdir(original_dir)
```

Using the helper:

```python
print(f"Starting in: {os.getcwd()}")

with temporary_working_directory("..") as current:
    print(f"Inside with: {os.getcwd()}")

print(f"Returned to: {os.getcwd()}")
```

### Flow of Execution in Generator Context Managers

1. Everything before the `yield` statement acts as `__enter__()`.
2. The expression in `yield <value>` is returned and bound to the `as` target.
3. When the `with` block finishes, execution resumes right after `yield`.
4. If an exception occurred inside the `with` block, it is raised at the point of `yield`. You must use a `try...finally` block inside the generator to ensure cleanup runs.

---

## Asynchronous Context Managers (`async with`)

In asynchronous programming with `asyncio`, acquiring or releasing resources often involves network or disk I/O. Asynchronous context managers use `__aenter__` and `__aexit__` instead of standard synchronous methods:

```python
import asyncio

class AsyncDatabaseConnection:
    def __init__(self, dsn: str):
        self.dsn = dsn

    async def __aenter__(self):
        print(f"Connecting to {self.dsn}...")
        await asyncio.sleep(0.05)  # Simulate network latency
        print("Connected.")
        return self

    async def __aexit__(self, exc_type, exc_val, exc_tb):
        print("Closing database connection...")
        await asyncio.sleep(0.01)
        print("Connection closed cleanly.")
        return False

async def main():
    async with AsyncDatabaseConnection("postgres://localhost:5432/app") as db:
        print("Executing queries...")

asyncio.run(main())
```

<div class="terminal-output">
Connecting to postgres://localhost:5432/app...
Connected.
Executing queries...
Closing database connection...
Connection closed cleanly.
</div>

You can also use `@contextlib.asynccontextmanager` to write generator-based async context managers.

---

## Common Standard Library Context Managers

Python's standard library provides many ready-to-use context managers:

| Module | Context Manager | Purpose |
| :--- | :--- | :--- |
| `builtins` | `open(file)` | Closes file handle upon block exit |
| `threading` | `lock` | Acquires and releases a mutex lock |
| `contextlib` | `suppress(*exceptions)` | Ignores specified exceptions |
| `contextlib` | `redirect_stdout(stream)` | Temporarily redirects `sys.stdout` |
| `contextlib` | `nullcontext(enter_result)` | Dummy context manager for conditional with statements |
| `pathlib` | `Path.open()` | Closes file handle opened via Path object |
| `decimal` | `decimal.localcontext()` | Sets temporary precision and rounding rules |

```python
from contextlib import redirect_stdout
import io

buffer = io.StringIO()
with redirect_stdout(buffer):
    print("This goes into the buffer, not the console!")
    print("Formatting report data...")

captured_output = buffer.getvalue()
print(f"Captured {len(captured_output)} characters.")
```

---

## Best Practices

1. **Always clean up in `finally`**: When creating generator context managers with `@contextmanager`, enclose the `yield` in a `try...finally` block to ensure cleanup runs even when exceptions occur.
2. **Do not abuse suppression**: Only suppress exceptions when you intentionally want to discard expected, non-fatal errors.
3. **Use `nullcontext` for conditional contexts**: When a context manager is only needed conditionally, avoid duplicating the `with` block logic:
    ```python
    from contextlib import nullcontext
    
    cm = open("output.txt", "w") if write_to_file else nullcontext()
    with cm as target:
        # Logic remains identical in both branches
        if target:
            target.write("Log line\n")
    ```
