# Windows FAQ

Answers to questions specifically concerning running, developing, and deploying Python on Microsoft Windows.

---

## How do I make Python scripts run without typing `python`?

Windows uses file associations and the `PATHEXT` environment variable:
1. Ensure `.PY` is listed in your system `PATHEXT` variable (`PATHEXT=.COM;.EXE;.BAT;.CMD;.PY`).
2. The standard installer associates `.py` files with the Python Launcher (`py.exe`).
3. You can now execute scripts by name directly in PowerShell or Command Prompt:
    ```powershell
    .\myscript.py --flag
    ```

---

## How do I create a standalone `.exe` from Python code?

To distribute your software to end-users who do not have Python installed, bundle your script, the Python runtime, and dependencies into an executable:

### Using PyInstaller

```bash
# 1. Install PyInstaller
pip install pyinstaller

# 2. Bundle script into a single self-contained .exe file
pyinstaller --onefile --windowed main.py
```

The resulting `dist/main.exe` contains the Python interpreter, your bytecodes, and all dependencies in a single portable file.

---

## What is a `*.pyd` file? Is it a DLL?

Yes! A `.pyd` file is simply a standard Windows Dynamic Link Library (`.dll`) with a different file extension.

CPython names C-extension modules with the `.pyd` extension to distinguish them from standard Windows DLLs. When you run `import mymath`, Python searches for `mymath.pyd` and calls its initialization entry point `PyInit_mymath()`.

---

## Reading and Writing the Windows Registry with `winreg`

The Python standard library includes the `winreg` module for interacting with the Windows Registry:

```python
import winreg

# Read a registry key
key_path = r"Software\Microsoft\Windows\CurrentVersion"
try:
    with winreg.OpenKey(winreg.HKEY_LOCAL_MACHINE, key_path) as key:
        program_files_dir, _ = winreg.QueryValueEx(key, "ProgramFilesDir")
        print(f"Program Files Directory: {program_files_dir}")
except FileNotFoundError:
    print("Registry key not found")
```

---

## Why does Python startup sometimes feel slow on Windows?

When Python imports a large package (like `scipy` or `azure-sdk`), it inspects and reads hundreds of small `.py` and `.pyc` files from disk.

On Windows, **Windows Defender Real-Time Protection** scans every file access. Adding your development directory or Python `.venv` folder to Windows Defender's **Excluded Folders** list can speed up Python startup and test execution by up to **300%**.
