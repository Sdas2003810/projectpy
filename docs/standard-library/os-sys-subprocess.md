# System and OS Modules (os, sys, subprocess, shutil)

Python provides standard library modules to interact directly with the operating system, execute system commands, manage files, and control program execution.

---

## 1. The `os` Module

The `os` module provides functions for interacting with the operating system.

### Common File and Directory Operations

| Function | Description |
| :--- | :--- |
| `os.getcwd()` | Returns the current working directory as a string |
| `os.chdir(path)` | Changes current working directory |
| `os.listdir(path)` | Returns a list of all entries in the directory |
| `os.mkdir(path)` | Creates a single new directory |
| `os.makedirs(path)` | Creates a directory and any missing parent directories |
| `os.remove(path)` | Deletes a file |
| `os.rmdir(path)` | Deletes an empty directory |

<div class="example-box">
<div class="example-title">Example: Working Directory and Environment Variables</div>

```python
import os

# Current working directory
cwd = os.getcwd()
print("Current Directory:", cwd)

# Accessing environment variables
user_home = os.environ.get("HOME") or os.environ.get("USERPROFILE")
print("User Home:", user_home)
```
</div>

---

## 2. The `sys` Module

The `sys` module provides variables and functions that interact directly with the Python interpreter.

### Important Attributes and Functions

| Property / Method | Description |
| :--- | :--- |
| `sys.argv` | List of command-line arguments passed to the script |
| `sys.exit(code)` | Exits the Python interpreter (default code 0 means success) |
| `sys.path` | List of directory strings where Python looks for modules |
| `sys.version` | Python interpreter version string |
| `sys.platform` | Operating system platform identifier (`win32`, `darwin`, `linux`) |

<div class="example-box">
<div class="example-title">Example: Inspecting sys Information</div>

```python
import sys

print("Python Version:", sys.version.split()[0])
print("Platform:", sys.platform)
print("Script Name:", sys.argv[0])
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
Python Version: 3.14.7
Platform: win32
Script Name: script.py
</div>
</div>

---

## 3. The `shutil` Module

While `os` works with individual files and directories, `shutil` provides high-level operations on files and collections of files.

<div class="example-box">
<div class="example-title">Example: Copying and Moving Files</div>

```python
import shutil

# Copy a single file
# shutil.copy("source.txt", "backup.txt")

# Copy an entire directory tree recursively
# shutil.copytree("src_folder", "backup_folder")

# Delete a directory and all of its contents
# shutil.rmtree("temp_folder")
```
</div>

---

## 4. The `subprocess` Module

The `subprocess` module is the standard way to start new processes, run external commands, and capture their input/output.

### Syntax of `subprocess.run()`
```python
subprocess.run(args, capture_output=False, text=False, check=False)
```

<div class="example-box">
<div class="example-title">Example: Executing a Shell Command</div>

```python
import subprocess

# Run command and capture output as text
result = subprocess.run(["python", "--version"], capture_output=True, text=True, check=True)

print("Return Code:", result.returncode)
print("Standard Output:", result.stdout.strip())
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
Return Code: 0
Standard Output: Python 3.14.7
</div>
</div>
