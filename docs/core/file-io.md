# File Handling and Context Managers

File handling in Python involves opening, reading, writing, and closing files on the local filesystem.

---

## The `with` Statement (Context Manager)

It is good practice to use the `with` keyword when dealing with file objects. The advantage is that the file is properly closed after its suite finishes, even if an exception is raised at any point:

### Syntax
```python
with open(filename, mode, encoding="utf-8") as file_handle:
    # file operations
```

<div class="example-box">
<div class="example-title">Example: Writing and Reading a Text File</div>

```python
# 1. Writing to a file
with open("notes.txt", "w", encoding="utf-8") as f:
    f.write("Line 1: Python documentation\n")
    f.write("Line 2: Production ready\n")

# 2. Reading the file back
with open("notes.txt", "r", encoding="utf-8") as f:
    content = f.read()

print(content)
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
Line 1: Python documentation
Line 2: Production ready
</div>
</div>

---

## File Access Modes

| Mode | Meaning | Creates File if Missing? | Truncates (Overwrites)? |
| :--- | :--- | :---: | :---: |
| `'r'` | Read only (default) | No (raises `FileNotFoundError`) | No |
| `'w'` | Write only | Yes | **Yes** |
| `'a'` | Append (writes to end) | Yes | No |
| `'r+'`| Read and Write | No | No |
| `'b'` | Binary mode (e.g. `'rb'`, `'wb'`) | - | - |

---

## Reading Files Line by Line

For large files, iterating directly over the file object avoids loading the entire file into RAM:

```python
with open("large_log.txt", "r", encoding="utf-8") as file:
    for line_number, line in enumerate(file, start=1):
        if "ERROR" in line:
            print(f"Line {line_number}: {line.strip()}")
```

---

## Modern Path Handling with `pathlib`

The `pathlib` module offers classes representing filesystem paths with semantics appropriate for different operating systems:

<div class="example-box">
<div class="example-title">Example: Using pathlib.Path</div>

```python
from pathlib import Path

# Create path object using / operator
data_dir = Path("data") / "reports"
file_path = data_dir / "summary.txt"

# Create directories if they do not exist
data_dir.mkdir(parents=True, exist_ok=True)

# Write and read text directly
file_path.write_text("All tests passing.", encoding="utf-8")
print("File Content:", file_path.read_text(encoding="utf-8"))
print("File Exists:", file_path.exists())
```
</div>
