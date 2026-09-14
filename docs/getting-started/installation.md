# Python Installation Guide

This guide walks you through downloading and installing Python on your computer.

---

## Operating System Instructions

Select your operating system:

=== "Windows"

    ### 1. Download Installer
    Visit the official Python website: [python.org/downloads](https://www.python.org/downloads/) and download the Windows installer executable.

    ### 2. Important Step: Add Python to PATH
    When running the installer, **check the checkbox** at the bottom of the first dialog:

    ```text
    [x] Add python.exe to PATH
    ```

    !!! warning "Important Note"
        If this checkbox is unchecked, typing `python` in Command Prompt or PowerShell will return an error stating that the command is not recognized.

    ### 3. Complete the Wizard
    - Select **Install Now**.
    - Click **Close** when the installation succeeds.

    ### 4. Verify Installation
    Open Command Prompt or PowerShell and enter:

    ```powershell
    python --version
    ```

=== "macOS"

    ### Option 1: Official Installer (Recommended for Beginners)
    1. Go to [python.org/downloads/macos](https://www.python.org/downloads/macos/).
    2. Download the latest macOS 64-bit installer package (`.pkg`).
    3. Run the installer and follow the on-screen steps.
    4. Open the Python folder in Applications and double-click `Install Certificates.command`.

    ### Option 2: Homebrew
    ```bash
    brew update
    brew install python
    ```

    ### Verify Installation
    ```bash
    python3 --version
    ```

=== "Linux"

    Most modern Linux distributions include Python 3 by default.

    ### Ubuntu / Debian
    ```bash
    sudo apt update
    sudo apt install python3 python3-pip python3-venv
    ```

    ### Fedora / Red Hat
    ```bash
    sudo dnf install python3 python3-pip
    ```

    ### Arch Linux
    ```bash
    sudo pacman -S python python-pip
    ```

    ### Verify Installation
    ```bash
    python3 --version
    ```

---

## Verification Test

To verify that both Python and its standard library are working correctly, run:

```bash
python -c "import sys; print('Python', sys.version.split()[0], 'installed successfully on', sys.platform)"
```
