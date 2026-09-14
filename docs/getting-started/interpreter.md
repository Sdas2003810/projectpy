# Using the Python Interpreter

Python code can be executed in two primary ways:
1. **Interactive Mode**: Running statements one line at a time in the interactive shell (REPL).
2. **Script Mode**: Saving code in a `.py` file and executing the entire file.

---

## 1. Interactive Mode (REPL)

The interactive interpreter is useful for testing short code snippets, checking documentation, and performing quick math calculations.

### Starting the Interpreter

Open your terminal or command prompt and type:

=== "Windows"
    ```powershell
    python
    # or py
    ```

=== "macOS / Linux"
    ```bash
    python3
    ```

The interpreter will start and display the primary prompt:

```text
Python 3.14.7 (main, Sep 2026) [MSC v.1944 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>>
```

The `>>>` prompt indicates that the interpreter is ready to receive commands.

<div class="example-box">
<div class="example-title">Example: Executing Statements in REPL</div>

```python
>>> 15 + 25
40
>>> language = "Python"
>>> language.upper()
'PYTHON'
```
</div>

### Special Variable: `_` (Last Printed Value)

In interactive mode, the special variable `_` holds the result of the last evaluated expression:

```python
>>> price = 100.50
>>> tax = 12.50
>>> price + tax
113.0
>>> _ + 10
123.0
```

### Exiting Interactive Mode
- Type `exit()` or `quit()` and press Enter.
- On Windows: Press `Ctrl + Z`, then Enter.
- On macOS / Linux: Press `Ctrl + D`.

---

## 2. Script Mode (Running `.py` Files)

For writing permanent programs, write your code in a text file with a `.py` extension.

<div class="example-box">
<div class="example-title">Example: greet.py</div>

```python
# greet.py
import sys

def main():
    if len(sys.argv) > 1:
        name = sys.argv[1]
        print(f"Hello, {name}")
    else:
        print("Hello, World")

if __name__ == "__main__":
    main()
```
</div>

### Running the Script

Open your terminal in the directory where `greet.py` is saved:

```bash
python greet.py
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
Hello, World
</div>

### Passing Arguments via Command Line

```bash
python greet.py Alice
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
Hello, Alice
</div>

---

## Understanding `sys.argv`

Command-line arguments passed to a Python script are stored in the list `sys.argv` provided by the `sys` module:

- `sys.argv[0]`: The script name (e.g. `'greet.py'`).
- `sys.argv[1]`: The first argument passed.
- `sys.argv[2]`: The second argument passed, and so on.

---

## The `if __name__ == "__main__":` Construct

In Python, every module has a built-in attribute called `__name__`:
- When a file is run directly from the terminal, `__name__` is set to `"__main__"`.
- When a file is imported into another file, `__name__` is set to the file's module name.

Using `if __name__ == "__main__":` ensures that the code inside this block only runs when the script is executed directly, not when it is imported as a module by another script.
