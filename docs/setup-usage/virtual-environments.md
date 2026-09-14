# Virtual Environments & Dependency Isolation

A **virtual environment** is a self-contained directory tree that contains a specific version of Python and an isolated set of packages.

Without virtual environments, installing packages globally creates dependency hell:
- Project A requires `urllib3==1.26` for legacy compatibility.
- Project B requires `urllib3>=2.0` for modern security features.
- If both share the global Python environment, upgrading for Project B immediately breaks Project A.

Virtual environments give every project its own private `site-packages` folder, completely eliminating cross-project conflicts.

---

## How Virtual Environments Work Under the Hood

When you create a virtual environment at `.venv`, Python creates a lightweight directory structure:

```
.venv/
├── pyvenv.cfg          # Configuration file pointing to the base Python installation
├── bin/ (or Scripts/ on Windows)
│   ├── python          # Symlink (or shim) to base python executable
│   ├── pip             # Virtual environment pip
│   └── activate        # Shell script to adjust PATH
└── lib/
    └── python3.13/
        └── site-packages/ # Where pip installs packages for this project only!
```

### The `pyvenv.cfg` File

The magic lies in `pyvenv.cfg`. When the `python` executable in `.venv/bin/` runs, it detects `pyvenv.cfg` in its parent directory. This triggers Python to set:
- `sys.prefix` to the `.venv` directory (instead of `/usr/local` or `C:\Python314`).
- `sys.path` to look in `.venv/lib/python3.13/site-packages`.

---

## Creating Environments with the Built-in `venv` Module

Python includes the `venv` module in the standard library (PEP 405):

```bash
# Create a virtual environment named .venv in the current directory
python -m venv .venv
```

### Common Flags

| Option | Description |
| :--- | :--- |
| `--system-site-packages` | Give the virtual environment access to global system packages |
| `--prompt <name>` | Customizes the shell prompt prefix (defaults to directory name) |
| `--clear` | Wipes the directory before creating the environment |
| `--without-pip` | Skips installing `pip` (for lightweight sandboxes) |

---

## Activating the Environment

Activation adjusts your current shell's `PATH` environment variable so that running `python` or `pip` automatically points to the virtual environment's executables.

| Operating System | Shell | Command |
| :--- | :--- | :--- |
| **Windows** | PowerShell | `.venv\Scripts\Activate.ps1` |
| **Windows** | Command Prompt (`cmd.exe`) | `.venv\Scripts\activate.bat` |
| **Linux / macOS** | `bash` / `zsh` | `source .venv/bin/activate` |
| **Linux / macOS** | `fish` | `source .venv/bin/activate.fish` |
| **Linux / macOS** | `csh` / `tcsh` | `source .venv/bin/activate.csh` |

Once activated, your command prompt displays the environment prefix:

```bash
(.venv) $ which python
/home/user/project/.venv/bin/python
```

To exit the virtual environment, run:

```bash
deactivate
```

---

## Running Without Activation (Best Practice for Automation & Scripts)

You do **not** have to activate an environment to use it! You can invoke the virtual environment's Python binary directly. It automatically uses its isolated `site-packages`:

=== "Linux / macOS"
    ```bash
    .venv/bin/python main.py
    .venv/bin/pip install requests
    ```

=== "Windows"
    ```powershell
    .venv\Scripts\python.exe main.py
    .venv\Scripts\pip.exe install requests
    ```

This is the preferred pattern for system daemons, systemd service units, Docker entrypoints, and CI/CD cron jobs.

---

## Modern Fast Alternatives: `uv`

While the built-in `venv` module is standard and reliable, the Python community has embraced **`uv`** (built in Rust by Astral):

```bash
# Install uv
pip install uv

# Create virtual environment in 10 milliseconds
uv venv

# Install dependencies 10-100x faster than pip
uv pip install -r requirements.txt
```
