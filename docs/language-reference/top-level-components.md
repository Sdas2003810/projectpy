# Top-Level Components

A Python *program* isn't one thing — it can be entered in several different ways, each with slightly different rules.

---

## Complete Programs

A complete Python program is a text file (usually `.py`) fed to the interpreter. The file is executed as a module in a fresh `__main__` namespace:

```bash
python3 myscript.py
```

The standard guard pattern:

```python
# myscript.py
def main():
    print("Hello from main!")

if __name__ == "__main__":
    main()
```

The `if __name__ == "__main__":` check ensures `main()` only runs when the script is executed directly — not when the file is imported as a module by something else.

---

## File Input

When Python reads a file, it parses the whole thing at once into an AST (Abstract Syntax Tree), then compiles and executes it. This is different from languages that interpret line by line.

You can compile and exec files manually:

```python
code = compile(open("script.py").read(), "script.py", "exec")
exec(code, {"__name__": "__main__"})
```

---

## Interactive Input

In the REPL, each line (or complete statement) is compiled and executed immediately. The result of expressions is displayed:

```python
>>> 1 + 2
3
>>> x = 10
>>> x * 3
30
>>> "hello"[::-1]
'olleh'
```

The REPL assigns the last expression result to the special variable `_`:

```python
>>> 2 ** 10
1024
>>> _ * 2
2048
```

---

## Expression Input

`eval()` evaluates a single expression from a string:

```python
result = eval("2 + 2 * 3")
print(result)   # 8

# With custom namespace
ns = {"x": 10, "y": 20}
print(eval("x + y", ns))   # 30
```

`exec()` executes arbitrary code (statements and expressions) from a string:

```python
code = """
def greet(name):
    return f"Hello, {name}!"

result = greet("World")
"""
ns = {}
exec(code, ns)
print(ns["result"])  # Hello, World!
```

!!! danger "Never `eval()` or `exec()` untrusted input"
    Both can execute arbitrary code. `eval("__import__('os').system('rm -rf /')")` is a real attack. Always use safe parsing (like `ast.literal_eval()` for data) instead.

---

## `ast.literal_eval()` — Safe Expression Parsing

For parsing Python literals (strings, numbers, tuples, lists, dicts, booleans, `None`) from strings safely:

```python
import ast

# Safe: only parses literals
data = ast.literal_eval('{"key": [1, 2, 3], "flag": True}')
print(data)  # {'key': [1, 2, 3], 'flag': True}

# Raises ValueError for non-literals:
ast.literal_eval("__import__('os')")  # ValueError
```

---

## The `__main__` Module

Python has a built-in `__main__` module — it's the environment where top-level code runs. You can import it:

```python
import __main__
print(dir(__main__))  # see everything defined at module level
```

The `python -m module` invocation runs a module's `__main__.py`:

```bash
python -m http.server 8000   # runs http/server/__main__.py
python -m venv myenv         # runs venv/__main__.py
python -m pytest             # runs pytest/__main__.py
```

This is the correct way to ship command-line tools inside a package.
