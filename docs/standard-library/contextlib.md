# `contextlib` — Utilities for `with`-statement Contexts

The `contextlib` module provides utilities for working with the `with` statement and context managers. While implementing custom context managers from scratch requires classes with `__enter__` and `__exit__` dunder methods, `contextlib` delivers convenient decorators, reusable helper contexts, and dynamic resource stacks.

---

## Core Decorators

### `@contextmanager`

Converts a generator function containing a single `yield` statement into a complete context manager:

```python
from contextlib import contextmanager
import time

@contextmanager
def benchmark(label: str):
    start = time.perf_counter()
    try:
        yield  # Code inside the `with` block executes here
    finally:
        elapsed = time.perf_counter() - start
        print(f"[{label}] Finished in {elapsed:.4f}s")

with benchmark("Matrix Multiplication"):
    matrix = [[i * j for j in range(500)] for i in range(500)]
```

<div class="terminal-output">
[Matrix Multiplication] Finished in 0.0215s
</div>

### `@asynccontextmanager`

The asynchronous equivalent for coroutines running in `asyncio`:

```python
from contextlib import asynccontextmanager
import asyncio

@asynccontextmanager
def async_timer(label: str):
    start = asyncio.get_event_loop().time()
    try:
        yield
    finally:
        elapsed = asyncio.get_event_loop().time() - start
        print(f"[{label}] Async task took {elapsed:.4f}s")
```

---

## Standard Context Manager Helpers

### 1. `contextlib.suppress(*exceptions)`

Replaces verbose `try...except Pass` patterns when safely ignoring anticipated, non-critical exceptions:

=== "With `suppress`"
    ```python
    from contextlib import suppress
    import os

    # Cleanly remove file without crashing if it doesn't exist:
    with suppress(FileNotFoundError):
        os.remove("temporary_cache.tmp")
    ```

=== "Traditional `try...except`"
    ```python
    import os

    try:
        os.remove("temporary_cache.tmp")
    except FileNotFoundError:
        pass
    ```

### 2. `contextlib.chdir(path)` (Python 3.11+)

Safely changes the current working directory for the duration of the `with` block, restoring the previous directory upon exit:

```python
from contextlib import chdir
import os

print(f"Current dir: {os.getcwd()}")

with chdir("/tmp"):
    print(f"Inside with: {os.getcwd()}")

print(f"Restored dir: {os.getcwd()}")
```

### 3. `contextlib.redirect_stdout` & `redirect_stderr`

Temporarily redirects standard output or error to any file-like object or string buffer:

```python
from contextlib import redirect_stdout
import io

buffer = io.StringIO()

with redirect_stdout(buffer):
    print("This message will be intercepted by the buffer.")
    help(str.upper)

captured = buffer.getvalue()
print(f"Captured {len(captured)} characters from help output.")
```

### 4. `contextlib.closing(thing)`

Wraps any object providing a `.close()` method that is not natively a context manager (e.g. `urllib.request.urlopen` or legacy database connections):

```python
from contextlib import closing
from urllib.request import urlopen

with closing(urlopen("https://www.python.org")) as page:
    for line in page.readlines()[:5]:
        print(line)
```

### 5. `contextlib.nullcontext(enter_result=None)`

A dummy context manager that does nothing. It is essential when a context manager is optional or conditional, avoiding duplicate code branches:

```python
from contextlib import nullcontext

def process_data(stream=None):
    # If no stream passed, write to stdout; otherwise enter the stream context
    target = nullcontext(sys.stdout) if stream is None else stream

    with target as out:
        out.write("Processing finished.\n")
```

---

## Dynamic Resource Management: `ExitStack`

When you need to manage a variable or unknown number of context managers at runtime (for example, opening an arbitrary list of files passed by the user), nesting `with` statements statically is impossible.

`ExitStack` maintains a dynamic stack of context managers and guarantees they are all cleaned up in reverse order:

```python
from contextlib import ExitStack
from pathlib import Path

def merge_files(input_filenames: list[str], output_filename: str):
    with ExitStack() as stack:
        # Dynamically open and manage all input files
        input_files = [
            stack.enter_context(open(fname, "r", encoding="utf-8"))
            for fname in input_filenames
        ]
        # Open output file
        out_file = stack.enter_context(open(output_filename, "w", encoding="utf-8"))

        # Write lines from each file
        for f in input_files:
            out_file.write(f.read())
            out_file.write("\n")

    # All files are guaranteed closed here, even if an exception was raised!
```

### Registering Callbacks in `ExitStack`

You can register arbitrary cleanup callbacks that execute upon exit:

```python
with ExitStack() as stack:
    stack.callback(print, "Cleanup step 1 executed")
    stack.callback(print, "Cleanup step 2 executed")
```

<div class="terminal-output">
Cleanup step 2 executed
Cleanup step 1 executed
</div>

For asynchronous code, use `AsyncExitStack`.

---

## Summary of `contextlib` Utilities

| Utility | Purpose | Introduced |
| :--- | :--- | :--- |
| `@contextmanager` | Turn generator into context manager | Python 2.5 |
| `@asynccontextmanager` | Turn async generator into async context manager | Python 3.7 |
| `suppress(*exc)` | Mute expected exceptions | Python 3.4 |
| `closing(obj)` | Calls `.close()` upon block exit | Python 2.5 |
| `nullcontext()` | Dummy no-op context manager | Python 3.7 |
| `redirect_stdout(s)` | Intercept stdout into a buffer/file | Python 3.4 |
| `redirect_stderr(s)` | Intercept stderr into a buffer/file | Python 3.5 |
| `chdir(path)` | Temporarily switch working directory | Python 3.11 |
| `ExitStack` | Programmatically enter dynamic number of contexts | Python 3.3 |
| `AsyncExitStack` | Dynamic async context manager stack | Python 3.7 |
