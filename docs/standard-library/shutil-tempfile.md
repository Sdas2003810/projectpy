# High-Level File Operations & Temporary Files: `shutil` and `tempfile`

While the `os` and `pathlib` modules provide low-level filesystem interactions and path abstractions, Python provides two dedicated standard library modules for high-level operations:
- **`shutil`**: High-level utility functions for copying, moving, archiving, and deleting entire directory trees.
- **`tempfile`**: Secure creation of temporary files and directories with automatic cleanup.

---

## The `shutil` Module

The `shutil` (shell utilities) module simplifies multi-file operations that would otherwise require complex recursive directory walks.

### Copying Files and Metadata

Python offers several functions for copying individual files, each preserving different levels of metadata:

| Function | Preserves Content | Preserves Permissions (`mode`) | Preserves Timestamps | Destination |
| :--- | :---: | :---: | :---: | :--- |
| `shutil.copyfile(src, dst)` | Yes | No | No | Must be file path |
| `shutil.copy(src, dst)` | Yes | Yes | No | File path or directory |
| `shutil.copy2(src, dst)` | Yes | Yes | Yes | File path or directory |

```python
import shutil

# copy2 preserves access/modification times and permissions
shutil.copy2("config.json", "backup/config_backup.json")
```

---

### Directory Trees: `copytree` and `rmtree`

#### Copying an Entire Directory Tree
To copy an entire directory recursively:

```python
from pathlib import Path
import shutil

src = Path("my_project")
dst = Path("backup/my_project_v1")

# dirs_exist_ok=True (Python 3.8+) allows copying into an existing destination directory
shutil.copytree(
    src,
    dst,
    dirs_exist_ok=True,
    ignore=shutil.ignore_patterns("*.pyc", "__pycache__", ".git")
)
```

#### Safely Deleting a Directory Tree
Use `shutil.rmtree()` to recursively delete an entire directory and all its contents:

```python
import shutil
from pathlib import Path

target_dir = Path("build/output")

if target_dir.exists():
    shutil.rmtree(target_dir)
```

---

### Moving and Renaming Files

`shutil.move()` recursively moves a file or directory to another location (similar to the Unix `mv` command):

```python
import shutil

# Move file or directory
shutil.move("reports/draft.pdf", "archive/2026/final.pdf")
```

---

### Disk Usage and System Path Resolution

#### Checking Available Disk Space
`shutil.disk_usage()` returns disk space statistics in bytes:

```python
import shutil

total, used, free = shutil.disk_usage(".")

print(f"Total: {total // (2**30)} GB")
print(f"Used:  {used // (2**30)} GB")
print(f"Free:  {free // (2**30)} GB")
```

#### Finding Executables on PATH
`shutil.which()` locates the system executable for a given command (equivalent to `which` or `where`):

```python
import shutil

git_path = shutil.which("git")
print("Git binary located at:", git_path)
# Output: Git binary located at: C:\Program Files\Git\cmd\git.EXE
```

---

### Archiving and Extraction (Zip, Tar, Gz)

`shutil` includes built-in support for creating and extracting archive formats without requiring manual interaction with `zipfile` or `tarfile`:

```python
import shutil

# 1. Create a zip archive of the 'docs' directory
archive_path = shutil.make_archive(
    base_name="backup_docs",  # Output filename (without extension)
    format="zip",             # Supported: 'zip', 'tar', 'gztar', 'bztar', 'xztar'
    root_dir="docs"           # Directory to compress
)
print("Created archive:", archive_path)  # backup_docs.zip

# 2. Unpack the archive into a destination directory
shutil.unpack_archive("backup_docs.zip", extract_dir="extracted_docs")
```

---

## The `tempfile` Module

Creating temporary files naively (e.g., `open('/tmp/test.txt', 'w')`) creates critical security vulnerabilities, including race conditions and symlink attacks. The standard library's `tempfile` module creates temporary files and directories safely using system-level secure creation flags.

### Temporary Directories (`TemporaryDirectory`)

The recommended pattern for temporary multi-file work is using `tempfile.TemporaryDirectory` with a context manager:

```python
import tempfile
from pathlib import Path

with tempfile.TemporaryDirectory(prefix="worker_job_") as temp_dir:
    temp_path = Path(temp_dir)
    print("Working inside temporary folder:", temp_path)
    
    # Create scratch files
    file1 = temp_path / "data.csv"
    file1.write_text("id,name\n1,Alpha\n2,Beta")
    
    # Process files...
    print("Files in temp dir:", list(temp_path.iterdir()))

# The temporary directory and all its contents are automatically deleted upon exiting the block
print("Directory still exists?", temp_path.exists())  # False
```

---

### Temporary Files (`NamedTemporaryFile`)

When an external utility requires a file path on disk, use `tempfile.NamedTemporaryFile`:

```python
import tempfile

with tempfile.NamedTemporaryFile(mode="w+t", suffix=".json", delete=True) as temp_file:
    # Write temporary content
    temp_file.write('{"status": "ok", "code": 200}')
    temp_file.flush()  # Ensure data is flushed to disk
    
    # The file has a real filesystem path
    print("Temporary file path:", temp_file.name)
    
    # Read back from the file
    temp_file.seek(0)
    print("Content read:", temp_file.read())

# File is automatically deleted upon leaving the 'with' block
```

### Python 3.12+ `delete_on_close`
On Windows, opening a file created with `delete=True` a second time while it is still open can raise a permission error (`PermissionError`). Python 3.12 introduced `delete_on_close=False`:

```python
import tempfile

# Allows closing the file and reopening it from another process before deleting
with tempfile.NamedTemporaryFile(delete_on_close=False) as tf:
    tf.write(b"Temporary binary payload")
    tf.close()
    
    # Can safely reopen or pass to another tool on Windows
    with open(tf.name, "rb") as reader:
        print("Re-read data:", reader.read())
```

---

## Security and Best Practices

| Recommendation | Detail |
| :--- | :--- |
| **Always use context managers** | Wrap `TemporaryDirectory` and `NamedTemporaryFile` in `with` blocks to guarantee cleanup even if exceptions occur. |
| **Use `shutil.copy2` for backups** | Regular `shutil.copy` drops modification timestamps; `copy2` preserves file metadata. |
| **Use `dirs_exist_ok=True`** | When copying into target directories that may already exist, specify `dirs_exist_ok=True` instead of deleting them first. |
| **Avoid hardcoded `/tmp` paths** | Use `tempfile.gettempdir()` to obtain the platform-specific temporary folder path (`C:\Users\...\AppData\Local\Temp` on Windows, `/tmp` on Linux). |
