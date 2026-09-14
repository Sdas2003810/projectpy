# Using Python on macOS

macOS is a Unix-based operating system with extensive support for Python development. However, configuring Python properly on macOS requires understanding the distinction between system-provided Python tools and developer-managed interpreters.

---

## The Golden Rule: Don't Touch System Python

Apple includes a minimal Python 3 runtime as part of the Command Line Tools (`/usr/bin/python3`).

!!! danger "Never Modify System Python"
    Never run `sudo pip install` against `/usr/bin/python3`. System tools and Xcode depend on the system runtime remaining untouched. Always install an isolated developer version.

---

## Recommended Installation Methods

### 1. Homebrew (Most Popular for Developers)

If you use the [Homebrew](https://brew.sh/) package manager:

```bash
# Install latest stable Python
brew install python

# Or install a specific version
brew install python@3.13
```

Homebrew installs Python into `/opt/homebrew/bin/python3` (on Apple Silicon M1/M2/M3/M4 Macs) or `/usr/local/bin/python3` (on Intel Macs) and keeps it updated automatically.

### 2. Official Installer from Python.org

Download the macOS Universal2 installer from [python.org](https://www.python.org/downloads/macos/).

!!! important "The SSL Certificates Gotcha on macOS"
    Python.org installers on macOS use their own private copy of OpenSSL and do not read system keychain root certificates by default.
    
    After installing from python.org, you **must run the certificate installer script**:
    
    ```bash
    /Applications/Python\ 3.13/Install\ Certificates.command
    ```
    
    Without this step, `urllib.request` and `pip` may fail with `SSL: CERTIFICATE_VERIFY_FAILED`.

### 3. Version Managers (`pyenv`)

If you work on multiple projects requiring different Python minor versions:

```bash
# Install pyenv via brew
brew install pyenv

# Install specific Python versions
pyenv install 3.11.9
pyenv install 3.12.5
pyenv install 3.13.0

# Set local version for current project directory
pyenv local 3.13.0
```

---

## Shell Configuration (`zsh`)

macOS uses `zsh` as its default shell. Ensure your user PATH is configured in `~/.zshrc` or `~/.zprofile`:

```bash
# Add Homebrew bin to PATH (if not already added)
eval "$(/opt/homebrew/bin/brew shellenv)"

# Reload configuration
source ~/.zshrc
```

Verify which interpreter is active:

```bash
which python3
python3 --version
```

---

## Creating Virtual Environments on macOS

Always isolate project dependencies inside a virtual environment:

```bash
# 1. Create virtual environment
python3 -m venv .venv

# 2. Activate environment
source .venv/bin/activate

# 3. Verify active Python points inside project
which python
# Output: /Users/username/project/.venv/bin/python
```

To exit the environment, run:

```bash
deactivate
```
