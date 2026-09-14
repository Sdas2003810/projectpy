# Interactive Debugging with `pdb`

The **Python Debugger** (`pdb`) provides an interactive debugging environment for Python programs. It supports setting source line breakpoints, single-stepping through code, inspecting stack frames, evaluating arbitrary Python expressions, and performing post-mortem analysis of crashes.

Since Python 3.7, you can invoke the debugger anywhere in your code using the built-in `breakpoint()` function (PEP 553), which replaces the older `import pdb; pdb.set_trace()`.

---

## The `breakpoint()` Built-In

To pause execution and open an interactive debugging prompt at a specific line, insert `breakpoint()`:

```python
def calculate_discount(price: float, discount_percent: float) -> float:
    breakpoint()  # Execution pauses here
    discount_amount = price * (discount_percent / 100)
    final_price = price - discount_amount
    return final_price

result = calculate_discount(120.0, 15.0)
print(f"Final: {result}")
```

When Python encounters `breakpoint()`, it stops execution and displays the interactive `(Pdb)` prompt:

```text
> /workspace/app.py(3)calculate_discount()
-> discount_amount = price * (discount_percent / 100)
(Pdb) 
```

### Disabling Breakpoints Globally
You can disable all `breakpoint()` calls in your environment without modifying source code by setting the `PYTHONBREAKPOINT` environment variable:

```bash
# Ignore all breakpoint() calls
PYTHONBREAKPOINT=0 python app.py

# Route breakpoint() to an alternative debugger like ipdb or pdbpp
PYTHONBREAKPOINT=ipdb.set_trace python app.py
```

---

## Essential `pdb` Command Reference

When paused at the `(Pdb)` prompt, you can inspect variables, navigate the call stack, and step through execution using single-letter shortcuts:

| Command | Shortcut | Description |
| :--- | :--- | :--- |
| `help` | `h` | Display available commands, or help on a specific command (`h step`). |
| `list` | `l` | List 11 lines of source code surrounding the current execution point. |
| `next` | `n` | Execute the current line and advance to the next line in the current function. |
| `step` | `s` | Step into the function call on the current line. |
| `until` | `unt` | Continue execution until reaching a line with a line number greater than current. |
| `return` | `r` | Continue execution until the current function returns. |
| `continue` | `c` | Continue normal program execution until the next breakpoint is hit. |
| `print` | `p <expr>` | Evaluate and print the value of an expression. |
| `pp` | `pp <expr>` | Pretty-print the value of an expression (useful for dicts and objects). |
| `whatis` | `whatis <expr>` | Print the type of an expression. |
| `where` | `w` | Print the full call stack trace, with the most recent frame at the bottom. |
| `up` | `u` | Move current frame context one level up the stack (toward caller). |
| `down` | `d` | Move current frame context one level down the stack (toward callee). |
| `break` | `b [loc]` | Set a new breakpoint at a line number or function name. |
| `clear` | `cl [bp#]` | Clear a specific breakpoint number or all breakpoints. |
| `quit` | `q` | Immediately terminate the Python program. |

---

## Stepping: `next` vs `step`

Understanding the difference between `n` (`next`) and `s` (`step`) is the core of effective debugging:

```python
def fetch_tax_rate(state: str) -> float:
    rates = {"CA": 0.0725, "NY": 0.04, "TX": 0.0625}
    return rates.get(state, 0.05)

def compute_total(price: float, state: str) -> float:
    rate = fetch_tax_rate(state)  # Line 6
    return price * (1 + rate)
```

If paused at Line 6:
- Pressing **`n` (`next`)**: Runs `fetch_tax_rate(state)` to completion and stops at Line 7. Use this when you trust the called function and don't need to inspect its internals.
- Pressing **`s` (`step`)**: Steps inside `fetch_tax_rate` and pauses at Line 2. Use this when you want to trace how the function computes its result.

---

## Setting Breakpoints Interactively

You do not have to edit source code to set breakpoints. You can launch your script through pdb from the command line:

```bash
python -m pdb myscript.py
```

Once inside, set breakpoints dynamically:

```text
# Break at line 42 of current file
(Pdb) b 42

# Break at line 15 of a specific file
(Pdb) b utils.py:15

# Break whenever function 'process_order' is called
(Pdb) b process_order

# Conditional breakpoint: break only if total exceeds 1000
(Pdb) b 42, total > 1000

# View all active breakpoints
(Pdb) b
Num Type         Disp Enb   Where
1   breakpoint   keep yes   at /workspace/app.py:42
        stop only if total > 1000
```

---

## Navigating the Call Stack with `where`, `up`, and `down`

When inspecting complex nested calls or caught exceptions, use `w` to view the call stack:

```text
(Pdb) w
  /workspace/main.py(24)<module>()
-> app.run()
  /workspace/server.py(105)run()
-> self.handle_request(req)
> /workspace/handlers.py(52)handle_request()
-> parse_payload(req.body)
```

The `>` indicator shows the currently selected frame. You can move up to the caller to check its variables without changing execution:

```text
(Pdb) u
> /workspace/server.py(105)run()
(Pdb) p req.headers
{'Content-Type': 'application/json'}
(Pdb) d
> /workspace/handlers.py(52)handle_request()
```

---

## Post-Mortem Debugging

When a script crashes with an unhandled exception, post-mortem debugging opens the debugger at the exact line and frame where the exception occurred, letting you inspect variable values at the moment of failure:

### Method 1: Command Line Flag
```bash
python -m pdb -c continue script_that_crashes.py
```

### Method 2: Programmatic Post-Mortem
```python
import pdb
import sys

def risky_operation():
    values = [10, 20, 0, 40]
    return [100 // v for v in values]

try:
    risky_operation()
except Exception:
    # Drops into post-mortem debugger automatically
    pdb.post_mortem(sys.exc_info()[2])
```

---

## Python 3.13 REPL Debugging Improvements

Starting in Python 3.13, the default interactive shell includes enhanced REPL capabilities:
- **Syntax-highlighted debugger outputs**: Source lines and tracebacks in pdb are rendered with ANSI syntax highlighting.
- **Improved command-line history**: Command navigation across debugger sessions persists through the system terminal history.
- **Direct Variable Inspection**: Expression evaluation in pdb displays formatting consistent with the modernized REPL.

---

## Summary Best Practices

1. **Use `breakpoint()`**: Avoid importing `pdb` directly; `breakpoint()` respects `PYTHONBREAKPOINT=0` in CI/CD pipelines.
2. **Prefer `pp` for Complex Data**: When inspecting dictionaries, database query results, or JSON objects, `pp var` produces readable, indented output.
3. **Use Conditional Breakpoints**: Instead of pressing `c` dozens of times in a loop, specify a condition: `b 28, item_id == 'target_99'`.
