# Python Standard Library Overview

Python adheres to a **"Batteries Included"** philosophy. The Python standard library provides a rich collection of built-in modules distributed with Python, allowing developers to build robust applications without installing third-party packages.

---

## Standard Library Modules by Category

### File Systems and Operating System
- `os`: Portable operating system interactions, file descriptor operations, working directory.
- `sys`: Interpreter-specific parameters and system functions.
- `pathlib`: Object-oriented filesystem paths.
- `shutil`: High-level file and directory operations (copying, moving, archiving).
- `subprocess`: Subprocess management and external command execution.

### Data Types and Collections
- `collections`: Specialized container types (`Counter`, `defaultdict`, `deque`, `namedtuple`).
- `itertools`: Functions creating iterators for efficient looping and combinatorics.
- `functools`: Higher-order functions and operations on callable objects (`@lru_cache`, `@wraps`).
- `heapq`: Heap queue algorithm (priority queue implementation).
- `bisect`: Array bisection algorithm for maintaining sorted lists.

### Numeric and Mathematical
- `math`: Mathematical functions for floating-point calculations.
- `cmath`: Mathematical functions for complex numbers.
- `random`: Pseudo-random number generators.
- `statistics`: Mathematical statistics functions (mean, median, stdev).
- `decimal`: Fast decimal floating-point arithmetic with fixed precision.
- `fractions`: Rational number arithmetic.

### Data Formats and Persistence
- `json`: JSON encoder and decoder.
- `csv`: Comma-Separated Values file reading and writing.
- `sqlite3`: Embedded SQLite relational database engine.
- `pickle`: Python object serialization.
- `xml.etree.ElementTree`: XML processing library.

### Text Processing
- `re`: Regular expression operations.
- `string`: Common string operations and templates.
- `difflib`: Computing deltas and sequence matching.
- `textwrap`: Text wrapping and filling.

### Dates, Times, and Calendars
- `datetime`: Basic date and time types.
- `time`: Time access and conversions.
- `zoneinfo`: IANA time zone support.
- `calendar`: General calendar-related functions.

### Testing and Debugging
- `unittest`: Unit testing framework.
- `doctest`: Test interactive Python examples in docstrings.
- `logging`: Flexible event logging framework for applications.
- `pdb`: Interactive source code debugger.
- `timeit`: Measure execution time of small code snippets.

---

## Further Reading

Select a specific standard library guide from the sidebar:
- [System and OS (os, sys, subprocess)](os-sys-subprocess.md)
- [Modern Filesystem Paths (pathlib)](pathlib.md)
- [Filesystem Utilities & Temporary Files (shutil, tempfile)](shutil-tempfile.md)
- [Priority Queues and Binary Search (heapq, bisect)](heapq-bisect.md)
- [Object Copying and Modification (copy)](copy-module.md)
- [Math, Random, and Statistics](math-and-random.md)
- [Date and Time](datetime-module.md)
- [Regular Expressions (re)](regex.md)
- [Data Handling (json, csv, sqlite3)](databases-and-json.md)
- [Collections and Itertools](collections-itertools.md)
- [Higher-Order Functions (functools)](functools.md)
- [Testing and Logging](testing-and-logging.md)
- [Mocking and Testing (unittest.mock)](unittest-mock.md)
- [Interactive Debugger (pdb)](pdb-debugger.md)
- [Threading and Multiprocessing](threading-multiprocessing.md)
- [Networking (socket, urllib, http)](networking.md)
- [Command-Line Argument Parsing (argparse)](argparse.md)
- [Cryptography and Secrets](hashlib-secrets.md)
- [Warnings and Deprecations](warnings-deprecation.md)

