# `pathlib` — Object-Oriented Filesystem Paths

The `pathlib` module (PEP 428) provides an object-oriented approach to working with filesystem paths. Instead of manipulating raw strings with `os.path.join`, `os.path.dirname`, and `os.path.splitext`, `pathlib` encapsulates paths as rich objects with intuitive methods and cross-platform compatibility.

---

## The Path Class Hierarchy

`pathlib` divides paths into two categories:

1. **Pure Paths**: Purely computational path objects that do not perform I/O operations (can be used on any OS, even without filesystem access).
    - `PurePath`: Root base class
    - `PurePosixPath`: POSIX-style paths (forward slashes `/`)
    - `PureWindowsPath`: Windows-style paths (drive letters, backslashes `\`)
2. **Concrete Paths**: Subclasses of pure paths that actually query and manipulate the underlying operating system's filesystem.
    - `Path`: Automatically instantiates `PosixPath` on Linux/macOS or `WindowsPath` on Windows.

```
                  +------------------+
                  |     PurePath     |
                  +--------+---------+
                           |
             +-------------+-------------+
             |                           |
    +--------v-------+          +--------v-------+
    | PurePosixPath  |          | PureWindowsPath|
    +--------+-------+          +--------+-------+
             |                           |
    +--------v-------+          +--------v-------+
    |   PosixPath    |          |   WindowsPath  |
    +--------+-------+          +--------+-------+
             \                           /
              \--- Both inherit from ---/
                            |
                     +------v-----+
                     |    Path    |
                     +------------+
```

---

## Creating Paths and the Slash Operator (`/`)

The slash operator `/` is overloaded to join path segments cleanly, eliminating manual slash handling:

```python
from pathlib import Path

# Current working directory
cwd = Path.cwd()

# User's home directory
home = Path.home()

# Joining path components with `/`
config_file = home / ".config" / "myapp" / "settings.json"
print(config_file)
# On Windows: C:\Users\Username\.config\myapp\settings.json
# On Linux:   /home/username/.config/myapp/settings.json
```

---

## Path Anatomy and Inspection

Every `Path` instance provides properties to inspect its constituent parts:

```python
p = Path("/var/log/nginx/access.log.gz")

print(p.name)       # "access.log.gz"       (Full filename)
print(p.stem)       # "access.log"          (Filename without final suffix)
print(p.suffix)     # ".gz"                 (Final file extension)
print(p.suffixes)   # [".log", ".gz"]       (All file extensions)
print(p.parent)     # Path("/var/log/nginx") (Parent directory)
print(p.anchor)     # "/"                   (Drive root or anchor)
print(p.is_absolute())  # True
```

### Navigating Ancestors

Use `.parents` sequence to traverse up directory levels:

```python
project_dir = Path("/home/alice/projects/backend/src/main.py")

for ancestor in project_dir.parents:
    print(ancestor)
# /home/alice/projects/backend/src
# /home/alice/projects/backend
# /home/alice/projects
# /home/alice
# /home
# /
```

---

## Filesystem Queries

Check file state without raising errors:

```python
path = Path("reports/quarterly.pdf")

if path.exists():
    print(f"Is file: {path.is_file()}")
    print(f"Is directory: {path.is_dir()}")
    print(f"Is symlink: {path.is_symlink()}")

    # File size and timestamps
    stat = path.stat()
    print(f"Size in bytes: {stat.st_size}")
    print(f"Last modified: {stat.st_mtime}")
```

### Resolving Paths

Transform relative paths or symlinks into canonical, absolute paths:

```python
rel_path = Path("./../docs/index.md")
absolute_clean = rel_path.resolve()
print(absolute_clean)
```

---

## Reading and Writing Files Directly

`Path` objects offer convenient one-shot methods to read and write without needing an explicit `open()` context manager for simple payloads:

=== "Text I/O"
    ```python
    file = Path("greeting.txt")

    # Write text with specified encoding
    file.write_text("Hello from Pathlib!", encoding="utf-8")

    # Read text
    content = file.read_text(encoding="utf-8")
    print(content)
    ```

=== "Binary I/O"
    ```python
    binary_file = Path("data.bin")

    # Write raw bytes
    binary_file.write_bytes(b"\x00\x01\x02\x03\x04")

    # Read raw bytes
    data = binary_file.read_bytes()
    print(data)
    ```

For streaming large files, use `Path.open()`:

```python
with path.open("r", encoding="utf-8") as f:
    for line in f:
        print(line.strip())
```

---

## Directory Operations and Searching

### Creating and Deleting Directories

```python
nested_dir = Path("build/cache/artifacts")

# Create full directory tree (mkdir -p equivalent)
nested_dir.mkdir(parents=True, exist_ok=True)

# Delete empty directory
nested_dir.rmdir()

# Delete a file
Path("temp.txt").unlink(missing_ok=True)
```

### Iterating and Globbing

```python
docs = Path("docs")

# List immediate children
for child in docs.iterdir():
    if child.is_file():
        print(f"File: {child.name}")

# Search for all markdown files in current directory
for md_file in docs.glob("*.md"):
    print(md_file.name)

# Recursive glob (search entire subtree)
for py_file in Path("src").rglob("*.py"):
    print(py_file)
```

### `Path.walk()` (Python 3.12+)

Added in Python 3.12 (PEP 701), `Path.walk()` provides an object-oriented equivalent to `os.walk()`:

```python
root = Path("src")

for dirpath, dirnames, filenames in root.walk():
    print(f"Directory: {dirpath}")
    for fname in filenames:
        print(f"  File: {fname}")
```

---

## Migration Cheatsheet: `os.path` vs `pathlib`

| Task | Legacy `os` / `os.path` | Modern `pathlib` |
| :--- | :--- | :--- |
| **Join paths** | `os.path.join(a, b)` | `Path(a) / b` |
| **Current directory** | `os.getcwd()` | `Path.cwd()` |
| **User directory** | `os.path.expanduser("~")` | `Path.home()` |
| **Check exists** | `os.path.exists(p)` | `p.exists()` |
| **Check file** | `os.path.isfile(p)` | `p.is_file()` |
| **Check directory** | `os.path.isdir(p)` | `p.is_dir()` |
| **Absolute path** | `os.path.abspath(p)` | `p.resolve()` |
| **Basename / Filename**| `os.path.basename(p)` | `p.name` |
| **Directory name** | `os.path.dirname(p)` | `p.parent` |
| **Extension** | `os.path.splitext(p)[1]` | `p.suffix` |
| **Create directory tree** | `os.makedirs(p, exist_ok=True)` | `p.mkdir(parents=True, exist_ok=True)` |
| **Delete file** | `os.remove(p)` | `p.unlink(missing_ok=True)` |
| **Rename / Move** | `os.rename(old, new)` | `old.rename(new)` |
