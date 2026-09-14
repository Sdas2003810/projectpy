# Command Line & Environment

The Python interpreter executable (`python` or `python3`) accepts a rich set of command-line flags, invocation modes, and environment variables that control execution, debugging, warning filters, and runtime behavior.

---

## Invocation Modes

The general syntax for invoking Python from a terminal is:

```bash
python [options] [-c cmd | -m mod | script.py | -] [args]
```

### 1. Script Execution (`script.py`)

Executes the specified Python file. Sets `sys.argv[0]` to the filename and `__name__` to `"__main__"`:

```bash
python app/server.py --port 8080
```

### 2. Module Execution (`-m <module>`)

Searches `sys.path` for the named module and executes its `__main__` entry point. **This is the recommended way to run Python tools**:

```bash
# Run pip within the active Python interpreter
python -m pip install requests

# Create a virtual environment
python -m venv .venv

# Launch an instant local web server
python -m http.server 8000

# Execute unit tests
python -m unittest discover tests/
```

!!! tip "Why `-m` is Safer Than Standalone Executables"
    Running `python -m pip` guarantees that packages are installed into the *exact* Python environment you are currently invoking, preventing common PATH mismatches where global `pip` installs packages to a different Python version than your active interpreter.

### 3. Inline Command Execution (`-c <command>`)

Executes arbitrary Python code passed as a string:

```bash
python -c "import sys; print(sys.version)"
```

### 4. Interactive Inspection (`-i`)

Runs the script or command, and then immediately drops into the interactive REPL with all global variables and state preserved for inspection:

```bash
python -i benchmark.py
# After benchmark finishes, you have access to its variables in the terminal
```

---

## Command-Line Options

| Flag | Description |
| :--- | :--- |
| `-c <cmd>` | Execute Python command string |
| `-m <mod>` | Run library module as a script |
| `-i` | Enter interactive mode after running script |
| `-O` | Basic optimizations (removes `assert` statements) |
| `-OO` | Discard `assert` statements and `__doc__` strings |
| `-v` | Verbose mode: trace all module imports as they happen |
| `-W <rule>` | Warning control (e.g. `-W error`, `-W ignore::DeprecationWarning`) |
| `-X dev` | Enable Python Development Mode (extra runtime checks) |
| `-X faulthandler` | Dump CPython traceback on low-level segmentation faults |
| `-X gil=0` | Disable the GIL in free-threaded builds (Python 3.13+) |
| `-B` | Do not write `.pyc` files on import |
| `-q` | Quiet: suppress copyright and version banner in REPL |

---

## Essential Environment Variables

You can configure Python globally by setting environment variables in your shell or Docker container:

### `PYTHONUNBUFFERED`

Forces `stdout` and `stderr` streams to be completely unbuffered.
In containerized environments (Docker, Kubernetes), buffered output often gets delayed or lost if the container crashes:

```dockerfile
# Standard Dockerfile best practice:
ENV PYTHONUNBUFFERED=1
```

### `PYTHONPATH`

A colon-separated (Unix) or semicolon-separated (Windows) list of directories prepended to `sys.path`. It tells Python where to look for imported modules:

```bash
# Linux / macOS
export PYTHONPATH="/home/user/my_lib:$PYTHONPATH"

# Windows PowerShell
$env:PYTHONPATH="C:\libs;$env:PYTHONPATH"
```

### `PYTHONDEVMODE`

Setting `PYTHONDEVMODE=1` enables Python Development Mode (equivalent to `-X dev`). It turns on memory allocation debuggers, enables `ResourceWarning` for unclosed files/sockets, and detects encoding bugs.

### `PYTHONUTF8`

Setting `PYTHONUTF8=1` (PEP 540) forces the Python runtime to use UTF-8 as the default text encoding for all OS operations, file reading, and console I/O, ignoring legacy platform-dependent codepages.
