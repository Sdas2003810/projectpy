# Using Python on Linux & Unix Platforms

Virtually every Linux distribution relies heavily on Python for operating system maintenance, package management tools (like `apt`, `dnf`, and `yum`), and system services. This guide covers distribution packages, PEP 668 restrictions, and building Python from source.

---

## Distribution Package Managers

Different Linux distributions split Python into modular packages:

=== "Ubuntu & Debian"
    ```bash
    sudo apt update
    # Note: Ubuntu splits virtual environment support into a separate package!
    sudo apt install python3 python3-pip python3-venv python3-dev
    ```

=== "Fedora / RHEL / CentOS"
    ```bash
    sudo dnf install python3 python3-pip python3-devel
    ```

=== "Arch Linux"
    ```bash
    sudo pacman -S python python-pip
    ```

---

## PEP 668: The "Externally Managed Environment" Error

On modern Linux distributions (Debian 12+, Ubuntu 23.04+, Fedora 38+), running `pip install <package>` globally outputs an error:

<div class="terminal-output">
error: externally-managed-environment

× This environment is externally managed
╰─> To install Python packages system-wide, try 'apt install python3-xyz',
    where xyz is the package you are trying to install.

    If you wish to install a non-Debian-packaged Python application,
    it may be easiest to use pipx.
</div>

### Why Does This Happen?

In the past, users running `sudo pip install` would overwrite system Python libraries maintained by `apt` or `dnf`, breaking system utilities. **PEP 668** prevents this collision.

### The Correct Solutions

1. **For Projects**: Always use a project virtual environment:
    ```bash
    python3 -m venv .venv
    source .venv/bin/activate
    pip install requests
    ```
2. **For Global CLI Tools (like Black, Ruff, or MkDocs)**: Use `pipx` to install tools into isolated per-application environments:
    ```bash
    sudo apt install pipx
    pipx ensurepath
    pipx install mkdocs-material
    ```

---

## Compiling Python from Source

When you need the latest Python release before your distribution packages it, or need a debug build, compile from source:

### 1. Install Build Dependencies

=== "Ubuntu / Debian"
    ```bash
    sudo apt update
    sudo apt install build-essential libssl-dev zlib1g-dev \
    libbz2-dev libreadline-dev libsqlite3-dev curl git \
    libncursesw5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev
    ```

=== "Fedora / RHEL"
    ```bash
    sudo dnf groupinstall "Development Tools"
    sudo dnf install openssl-devel bzip2-devel libffi-devel \
    zlib-devel readline-devel sqlite-devel
    ```

### 2. Configure and Compile

```bash
# Download and unpack release tarball
wget https://www.python.org/ftp/python/3.13.0/Python-3.13.0.tgz
tar -xf Python-3.13.0.tgz
cd Python-3.13.0

# Configure with production optimizations
./configure --enable-optimizations --with-lto

# Compile using all available CPU cores
make -j $(nproc)
```

### 3. Install with `altinstall`

!!! danger "Always Use `make altinstall`, Never `make install`!"
    Running `sudo make install` overwrites the system `/usr/bin/python3` binary, breaking your Linux operating system!
    
    `sudo make altinstall` installs the binary as `/usr/local/bin/python3.13` without replacing the system `python3` alias:

```bash
sudo make altinstall
```

Verify the installation:

```bash
python3.13 --version
```

---

## Shebang Conventions & Executable Permissions

To make a Python script directly executable from the Linux terminal:

1. Add the portable shebang line as the first line of the file:
    ```python
    #!/usr/bin/env python3
    import sys
    print(f"Executed via: {sys.executable}")
    ```
2. Grant execute permissions with `chmod`:
    ```bash
    chmod +x script.py
    ```
3. Run the script directly:
    ```bash
    ./script.py
    ```
