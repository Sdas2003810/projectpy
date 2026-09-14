# Library and Extension FAQ

Answers to common questions regarding Python's standard library capabilities, system integration, and interfacing with low-level compiled code.

---

## How do I run external shell commands from Python?

Always use the **`subprocess`** module. Avoid legacy functions like `os.system()` or `os.popen()`, which are vulnerable to shell injection attacks.

```python
import subprocess

# Run command safely without shell interpretation
result = subprocess.run(
    ["git", "status", "--porcelain"],
    capture_output=True,
    text=True,
    check=True  # Automatically raises CalledProcessError on non-zero exit!
)

print(result.stdout)
```

!!! danger "Never pass untrusted user strings to `shell=True`"
    Setting `shell=True` invokes the system shell (`/bin/sh` or `cmd.exe`). If user-controlled strings are formatted into the command, attackers can inject arbitrary shell commands! Always pass arguments as a list of strings (`["cmd", "arg1", "arg2"]`).

---

## How do I interface with external C/C++ code?

Python provides several powerful mechanisms depending on your performance and ergonomics requirements:

| Technology | Best For | Requires C Compiler? |
| :--- | :--- | :--- |
| **`ctypes`** | Calling existing shared C libraries (`.dll`, `.so`) directly | No |
| **`cffi`** | Modern, safer C Foreign Function Interface | Optional |
| **Cython** | Compiling Python-like code to ultra-fast C extensions | Yes |
| **CPython C-API** | Deep low-level interpreter integration and extension modules | Yes |
| **PyO3** | Writing high-performance Python extensions in Rust | Yes (Rust compiler) |

### Quick Example with `ctypes`

```python
import ctypes

# Call C standard library directly from pure Python!
# On Windows: ctypes.cdll.msvcrt
# On Linux:   ctypes.CDLL("libc.so.6")
import platform

if platform.system() == "Windows":
    libc = ctypes.cdll.msvcrt
else:
    libc = ctypes.CDLL("libc.so.6")

libc.puts(b"Hello from C standard library puts() called by Python!")
```

---

## How do I parse XML and HTML in Python?

### XML Parsing with `xml.etree.ElementTree`

The built-in `ElementTree` module provides a fast, lightweight XML parser:

```python
import xml.etree.ElementTree as ET

xml_data = """<library>
    <book id="1"><title>Fluent Python</title></book>
    <book id="2"><title>Effective Python</title></book>
</library>"""

root = ET.fromstring(xml_data)
for book in root.findall("book"):
    print(book.find("title").text)
```

### HTML Parsing

Python includes `html.parser` for basic tokenization. For modern web scraping that tolerates broken HTML, install **BeautifulSoup4** (`pip install beautifulsoup4`).

---

## How do I generate random numbers cryptographically?

Use the standard library **`secrets`** module, not `random`:

```python
import secrets

# Cryptographically secure random token:
secure_token = secrets.token_hex(32)
```
