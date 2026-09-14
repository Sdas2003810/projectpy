# Using Python on Windows

Python has first-class support for Microsoft Windows. This guide covers installation best practices, the powerful **Python Launcher for Windows (`py.exe`)**, PowerShell execution policies, and Windows-specific nuances.

---

## Installation Methods

### 1. The Official Installer (Recommended)

Download the official executable installer from [python.org](https://www.python.org/downloads/windows/).

!!! important "Crucial Installer Options"
    During installation, ensure you check the following boxes:
    
    1. **"Add python.exe to PATH"**: Ensures commands like `python` and `pip` work in PowerShell and Command Prompt.
    2. **"Use admin privileges when installing py.exe"**: Installs the launcher system-wide for all users.
    3. At the end of the installation, click **"Disable path length limit"** to remove the legacy 260-character MAX_PATH limit in Windows.

### 2. Windows Package Manager (`winget`)

If you prefer installing via the command line:

```powershell
# Install the latest Python release
winget install Python.Python.3.13
```

---

## The Python Launcher for Windows (`py.exe`)

The Windows installer includes `py.exe`, a dedicated launcher installed directly into `C:\Windows\py.exe` (which is always on the system PATH).

The launcher solves the problem of having multiple Python versions installed simultaneously:

```powershell
# List all installed Python versions (both 32-bit and 64-bit)
py -0p

# Run script with a specific Python version
py -3.11 main.py
py -3.12 main.py
py -3.13 main.py

# Launch interactive REPL with latest version
py
```

### Shebang Line Support

Even though Windows does not natively support Unix shebang lines (`#!/usr/bin/env python3`), the `py.exe` launcher parses them automatically:

```python
#! /usr/bin/env python3.12
# When you double-click this file or run `py script.py`,
# the launcher automatically routes execution to Python 3.12!
import sys
print(sys.version)
```

---

## Virtual Environments on Windows

To create and activate isolated project dependencies:

```powershell
# 1. Create a virtual environment inside `.venv`
py -m venv .venv

# 2. Activate the virtual environment
# In PowerShell:
.venv\Scripts\Activate.ps1

# In Command Prompt (cmd.exe):
.venv\Scripts\activate.bat
```

### Fixing PowerShell Execution Policy Error

If PowerShell displays an error stating that `Activate.ps1 cannot be loaded because running scripts is disabled on this system`, update your user execution policy:

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

---

## Windows-Specific Gotchas & Tips

### 1. Backslashes in Path Strings

Windows paths use backslashes (`\`), which act as escape characters in Python strings (`\n`, `\t`):

```python
# WRONG: '\n' is interpreted as a newline character!
# bad_path = "C:\new_folder\test.txt"

# SOLUTION A: Use raw string literals (prefix with r)
raw_path = r"C:\new_folder\test.txt"

# SOLUTION B: Use forward slashes (Python handles them automatically on Windows)
clean_path = "C:/new_folder/test.txt"

# SOLUTION C (Best Practice): Use pathlib
from pathlib import Path
safe_path = Path("C:/new_folder") / "test.txt"
```

### 2. `.py` vs `.pyw` File Extensions

- **`.py` files**: Executed by `python.exe`. A terminal console window opens to display output.
- **`.pyw` files**: Executed by `pythonw.exe`. Runs silently in the background without opening a terminal window. This is designed for GUI desktop applications (Tkinter, PyQt, PySide).

### 3. UTF-8 and Legacy Code Pages

Legacy Windows systems use legacy codepages (such as `cp1252`). Starting in Python 3.7+, Python on Windows defaults to UTF-8 for console and file I/O where possible. Always specify `encoding="utf-8"` explicitly when calling `open()`:

```python
with open("data.txt", "w", encoding="utf-8") as f:
    f.write("Unicode text: 🚀 Python")
```
