---
title: Standard Library Coverage Status
description: Complete status of all Python 3 standard library modules — what ProjectPy covers, what's partial, and what's planned.
---

# Standard Library Coverage Status

Python ships with **over 230 built-in modules** across its standard library. This page tracks what ProjectPy currently documents, what's partially covered, and what's planned for future contribution.

<div class="coverage-summary-grid">
  <div class="coverage-stat covered">
    <svg class="stat-icon" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path fill="currentColor" d="M12 2C6.5 2 2 6.5 2 12s4.5 10 10 10 10-4.5 10-10S17.5 2 12 2m-2 15-5-5 1.41-1.41L10 14.17l7.59-7.59L19 8z"/></svg>
    <div class="stat-number">~50</div>
    <div class="stat-label">Fully Covered</div>
  </div>
  <div class="coverage-stat partial">
    <svg class="stat-icon" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path fill="currentColor" d="M12 20a8 8 0 0 0 8-8 8 8 0 0 0-8-8 8 8 0 0 0-8 8 8 8 0 0 0 8 8m0-18a10 10 0 0 1 10 10 10 10 0 0 1-10 10C6.47 22 2 17.5 2 12A10 10 0 0 1 12 2m.5 5v5.25l4.5 2.67-.75 1.23L11 13V7z"/></svg>
    <div class="stat-number">~15</div>
    <div class="stat-label">Partially Covered</div>
  </div>
  <div class="coverage-stat notyet">
    <svg class="stat-icon" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path fill="currentColor" d="M12 20a8 8 0 0 1-8-8 8 8 0 0 1 8-8 8 8 0 0 1 8 8 8 8 0 0 1-8 8m0-18A10 10 0 0 0 2 12a10 10 0 0 0 10 10 10 10 0 0 0 10-10A10 10 0 0 0 12 2"/></svg>
    <div class="stat-number">~170</div>
    <div class="stat-label">Planned / Needed</div>
  </div>
</div>

!!! tip "Want to help?"
    Every planned module is an opportunity to contribute! See our [How to Contribute](community/contributing.md) guide to get started — no prior open-source experience required.

---

## Legend

| Badge | Meaning |
|---|---|
| <span class="cov-badge covered">:material-check-circle: Covered</span> | Has a dedicated guide with examples on this site |
| <span class="cov-badge partial">:material-clock-outline: Partial</span> | Mentioned or briefly covered within another guide |
| <span class="cov-badge notyet">:material-circle-outline: Planned</span> | No documentation exists here yet — **contribute!** |
| <span class="cov-badge removed">:material-minus-circle-outline: Removed</span> | Removed from CPython in 3.11–3.14 (PEP 594 dead batteries) |

---

## Text Processing

| Module | Status | Guide |
|--------|--------|-------|
| `string` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [String Module](standard-library/string-module.md) |
| `re` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Regular Expressions](standard-library/regex.md) |
| `difflib` | <span class="cov-badge partial">:material-clock-outline: Partial</span> | Mentioned in overview |
| `textwrap` | <span class="cov-badge partial">:material-clock-outline: Partial</span> | Mentioned in overview |
| `unicodedata` | <span class="cov-badge partial">:material-clock-outline: Partial</span> | [Unicode HOWTO](howto/unicode-howto.md) |
| `readline` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `rlcompleter` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |

---

## Binary Data Services

| Module | Status | Guide |
|--------|--------|-------|
| `struct` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `codecs` | <span class="cov-badge partial">:material-clock-outline: Partial</span> | [Unicode HOWTO](howto/unicode-howto.md) |

---

## Data Types & Collections

| Module | Status | Guide |
|--------|--------|-------|
| `datetime` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Date and Time](standard-library/datetime-module.md) |
| `zoneinfo` | <span class="cov-badge partial">:material-clock-outline: Partial</span> | [Date and Time](standard-library/datetime-module.md) |
| `calendar` | <span class="cov-badge partial">:material-clock-outline: Partial</span> | [Date and Time](standard-library/datetime-module.md) |
| `collections` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Collections and Itertools](standard-library/collections-itertools.md) |
| `collections.abc` | <span class="cov-badge partial">:material-clock-outline: Partial</span> | [Collections and Itertools](standard-library/collections-itertools.md) |
| `heapq` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [heapq & bisect](standard-library/heapq-bisect.md) |
| `bisect` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [heapq & bisect](standard-library/heapq-bisect.md) |
| `array` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `weakref` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `types` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `copy` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Object Copying](standard-library/copy-module.md) |
| `pprint` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `reprlib` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `enum` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Enum](standard-library/enum.md) |
| `graphlib` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |

---

## Numeric and Mathematical Modules

| Module | Status | Guide |
|--------|--------|-------|
| `numbers` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `math` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Math, Random, Statistics](standard-library/math-and-random.md) |
| `cmath` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Math, Random, Statistics](standard-library/math-and-random.md) |
| `decimal` | <span class="cov-badge partial">:material-clock-outline: Partial</span> | [Math, Random, Statistics](standard-library/math-and-random.md) |
| `fractions` | <span class="cov-badge partial">:material-clock-outline: Partial</span> | [Math, Random, Statistics](standard-library/math-and-random.md) |
| `random` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Math, Random, Statistics](standard-library/math-and-random.md) |
| `statistics` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Math, Random, Statistics](standard-library/math-and-random.md) |

---

## Functional Programming Modules

| Module | Status | Guide |
|--------|--------|-------|
| `itertools` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Collections and Itertools](standard-library/collections-itertools.md) |
| `functools` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Functools](standard-library/functools.md) |
| `operator` | <span class="cov-badge partial">:material-clock-outline: Partial</span> | [Functools](standard-library/functools.md) |

---

## File and Directory Access

| Module | Status | Guide |
|--------|--------|-------|
| `pathlib` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Pathlib](standard-library/pathlib.md) |
| `os.path` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [OS, Sys, Subprocess](standard-library/os-sys-subprocess.md) |
| `fileinput` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `stat` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `filecmp` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `tempfile` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [shutil & tempfile](standard-library/shutil-tempfile.md) |
| `glob` | <span class="cov-badge partial">:material-clock-outline: Partial</span> | [OS, Sys, Subprocess](standard-library/os-sys-subprocess.md) |
| `fnmatch` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `linecache` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `shutil` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [shutil & tempfile](standard-library/shutil-tempfile.md) |

---

## Data Persistence and Databases

| Module | Status | Guide |
|--------|--------|-------|
| `pickle` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [IO and Serialization](standard-library/io-and-serialization.md) |
| `shelve` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `marshal` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `dbm` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `sqlite3` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Data Formats](standard-library/databases-and-json.md) |

---

## Data Compression and Archiving

| Module | Status | Guide |
|--------|--------|-------|
| `zlib` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `gzip` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `bz2` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `lzma` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `zipfile` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `tarfile` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |

---

## File Formats

| Module | Status | Guide |
|--------|--------|-------|
| `csv` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Data Formats](standard-library/databases-and-json.md) |
| `configparser` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `tomllib` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `netrc` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `plistlib` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |

---

## Cryptographic Services

| Module | Status | Guide |
|--------|--------|-------|
| `hashlib` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Hashing and Secrets](standard-library/hashlib-secrets.md) |
| `hmac` | <span class="cov-badge partial">:material-clock-outline: Partial</span> | [Hashing and Secrets](standard-library/hashlib-secrets.md) |
| `secrets` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Hashing and Secrets](standard-library/hashlib-secrets.md) |

---

## Generic Operating System Services

| Module | Status | Guide |
|--------|--------|-------|
| `os` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [OS, Sys, Subprocess](standard-library/os-sys-subprocess.md) |
| `io` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [IO and Serialization](standard-library/io-and-serialization.md) |
| `time` | <span class="cov-badge partial">:material-clock-outline: Partial</span> | [Date and Time](standard-library/datetime-module.md) |
| `argparse` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Argument Parsing](standard-library/argparse.md) |
| `getopt` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `logging` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Testing and Logging](standard-library/testing-and-logging.md) |
| `logging.config` | <span class="cov-badge partial">:material-clock-outline: Partial</span> | [Testing and Logging](standard-library/testing-and-logging.md) |
| `logging.handlers` | <span class="cov-badge partial">:material-clock-outline: Partial</span> | [Testing and Logging](standard-library/testing-and-logging.md) |
| `getpass` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `platform` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `errno` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `ctypes` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `warnings` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Warnings and Deprecations](standard-library/warnings-deprecation.md) |
| `dataclasses` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Dataclasses](standard-library/dataclasses.md) |
| `contextlib` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Context Management](standard-library/contextlib.md) |
| `abc` | <span class="cov-badge partial">:material-clock-outline: Partial</span> | Mentioned in OOP tutorial |
| `atexit` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `traceback` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `gc` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `inspect` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `sys` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [OS, Sys, Subprocess](standard-library/os-sys-subprocess.md) |
| `sysconfig` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `builtins` | <span class="cov-badge partial">:material-clock-outline: Partial</span> | [Built-in Functions](reference/built-in-functions.md) |
| `__future__` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |

---

## Concurrent Execution

| Module | Status | Guide |
|--------|--------|-------|
| `threading` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Threading and Multiprocessing](standard-library/threading-multiprocessing.md) |
| `multiprocessing` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Threading and Multiprocessing](standard-library/threading-multiprocessing.md) |
| `multiprocessing.shared_memory` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `concurrent.futures` | <span class="cov-badge partial">:material-clock-outline: Partial</span> | [Threading and Multiprocessing](standard-library/threading-multiprocessing.md) |
| `subprocess` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [OS, Sys, Subprocess](standard-library/os-sys-subprocess.md) |
| `sched` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `queue` | <span class="cov-badge partial">:material-clock-outline: Partial</span> | [Threading and Multiprocessing](standard-library/threading-multiprocessing.md) |
| `_thread` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |

---

## Networking and Interprocess Communication

| Module | Status | Guide |
|--------|--------|-------|
| `asyncio` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Asyncio and Concurrency](advanced/asyncio-concurrency.md) |
| `socket` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Networking](standard-library/networking.md) |
| `ssl` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `select` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `selectors` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `signal` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `mmap` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |

---

## Internet Protocols and Support

| Module | Status | Guide |
|--------|--------|-------|
| `urllib` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Networking](standard-library/networking.md) |
| `urllib.request` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [urllib HOWTO](howto/urllib-howto.md) |
| `urllib.parse` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Networking](standard-library/networking.md) |
| `urllib.error` | <span class="cov-badge partial">:material-clock-outline: Partial</span> | [urllib HOWTO](howto/urllib-howto.md) |
| `http.client` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Networking](standard-library/networking.md) |
| `http.server` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Networking](standard-library/networking.md) |
| `http.cookies` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `http.cookiejar` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `json` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Data Formats](standard-library/databases-and-json.md) |
| `html` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `html.parser` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `xml.etree.ElementTree` | <span class="cov-badge partial">:material-clock-outline: Partial</span> | [IO and Serialization](standard-library/io-and-serialization.md) |
| `xml.dom` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `xml.sax` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `email` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `smtplib` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `imaplib` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `ftplib` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `poplib` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `uuid` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `ipaddress` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |

---

## Debugging and Profiling

| Module | Status | Guide |
|--------|--------|-------|
| `pdb` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Interactive Debugger](standard-library/pdb-debugger.md) |
| `faulthandler` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `timeit` | <span class="cov-badge partial">:material-clock-outline: Partial</span> | [Testing and Logging](standard-library/testing-and-logging.md) |
| `cProfile` | <span class="cov-badge partial">:material-clock-outline: Partial</span> | [Perf Profiling](howto/perf_profiling.md) |
| `profile` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `pstats` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `tracemalloc` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |

---

## Testing

| Module | Status | Guide |
|--------|--------|-------|
| `unittest` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Testing and Logging](standard-library/testing-and-logging.md) |
| `unittest.mock` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Mocking and Testing](standard-library/unittest-mock.md) |
| `doctest` | <span class="cov-badge partial">:material-clock-outline: Partial</span> | [Testing and Logging](standard-library/testing-and-logging.md) |

---

## Software Packaging and Distribution

| Module | Status | Guide |
|--------|--------|-------|
| `venv` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Virtual Environments](packaging/virtual-environments-venv.md) |
| `zipimport` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `importlib` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `importlib.metadata` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `importlib.resources` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `pkgutil` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |

---

## Python Runtime Services

| Module | Status | Guide |
|--------|--------|-------|
| `typing` | <span class="cov-badge covered">:material-check-circle: Covered</span> | [Typing Module](standard-library/typing-module.md) |
| `typing_extensions` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `annotationlib` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `ast` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `dis` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `token` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `tokenize` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `symtable` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `compileall` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `py_compile` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `code` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |
| `codeop` | <span class="cov-badge notyet">:material-circle-outline: Planned</span> | — |

---

## Superseded & Removed Modules

!!! warning "Removed from CPython"
    These modules were removed as part of [PEP 594](https://peps.python.org/pep-0594/) and related deprecation cycles. Do **not** use them in new code.

| Module | Status | Removed In |
|--------|--------|------------|
| `aifc` | <span class="cov-badge removed">:material-minus-circle-outline: Removed</span> | Python 3.13 |
| `audioop` | <span class="cov-badge removed">:material-minus-circle-outline: Removed</span> | Python 3.13 |
| `cgi` | <span class="cov-badge removed">:material-minus-circle-outline: Removed</span> | Python 3.13 |
| `cgitb` | <span class="cov-badge removed">:material-minus-circle-outline: Removed</span> | Python 3.13 |
| `chunk` | <span class="cov-badge removed">:material-minus-circle-outline: Removed</span> | Python 3.13 |
| `crypt` | <span class="cov-badge removed">:material-minus-circle-outline: Removed</span> | Python 3.13 |
| `imghdr` | <span class="cov-badge removed">:material-minus-circle-outline: Removed</span> | Python 3.13 |
| `mailcap` | <span class="cov-badge removed">:material-minus-circle-outline: Removed</span> | Python 3.13 |
| `msilib` | <span class="cov-badge removed">:material-minus-circle-outline: Removed</span> | Python 3.13 |
| `nis` | <span class="cov-badge removed">:material-minus-circle-outline: Removed</span> | Python 3.13 |
| `nntplib` | <span class="cov-badge removed">:material-minus-circle-outline: Removed</span> | Python 3.13 |
| `ossaudiodev` | <span class="cov-badge removed">:material-minus-circle-outline: Removed</span> | Python 3.13 |
| `pipes` | <span class="cov-badge removed">:material-minus-circle-outline: Removed</span> | Python 3.13 |
| `sndhdr` | <span class="cov-badge removed">:material-minus-circle-outline: Removed</span> | Python 3.13 |
| `spwd` | <span class="cov-badge removed">:material-minus-circle-outline: Removed</span> | Python 3.13 |
| `sunau` | <span class="cov-badge removed">:material-minus-circle-outline: Removed</span> | Python 3.13 |
| `telnetlib` | <span class="cov-badge removed">:material-minus-circle-outline: Removed</span> | Python 3.13 |
| `uu` | <span class="cov-badge removed">:material-minus-circle-outline: Removed</span> | Python 3.13 |
| `xdrlib` | <span class="cov-badge removed">:material-minus-circle-outline: Removed</span> | Python 3.13 |
| `smtpd` | <span class="cov-badge removed">:material-minus-circle-outline: Removed</span> | Python 3.12 |

---

## Want to Contribute a Missing Module?

Missing modules are great first contributions. Here's what a good module guide includes:

1. **What the module does** — a plain-English explanation
2. **When you'd use it** — real-world use case
3. **Worked examples** — copyable, runnable Python 3.12+ code
4. **Common pitfalls** — "gotchas" that trip up beginners
5. **Links** — reference to the official CPython docs

:material-arrow-right: Read the **[How to Contribute Guide](community/contributing.md)** to get started!
