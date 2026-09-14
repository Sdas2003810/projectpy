# Testing and Logging (unittest, doctest, logging)

Writing production-grade software requires structured automated verification and observable diagnostic logging. Python provides `unittest`, `doctest`, and `logging` out of the box.

---

## 1. Unit Testing with `unittest`

The `unittest` module provides a rich set of tools for constructing and running test suites.

### Common Assertions

| Method | Checks That |
| :--- | :--- |
| `self.assertEqual(a, b)` | $a == b$ |
| `self.assertNotEqual(a, b)` | $a \ne b$ |
| `self.assertTrue(x)` | `bool(x) is True` |
| `self.assertFalse(x)` | `bool(x) is False` |
| `self.assertIn(item, list)` | `item in list` |
| `self.assertRaises(exc)` | Function raises specified exception |

<div class="example-box">
<div class="example-title">Example: Writing a Unit Test Suite</div>

```python
import unittest

def add(x: int, y: int) -> int:
    return x + y

def divide(x: float, y: float) -> float:
    if y == 0:
        raise ValueError("Cannot divide by zero")
    return x / y

class TestMathOperations(unittest.TestCase):

    def test_add(self):
        self.assertEqual(add(2, 3), 5)
        self.assertEqual(add(-1, 1), 0)

    def test_divide_zero(self):
        with self.assertRaises(ValueError):
            divide(10, 0)

if __name__ == "__main__":
    unittest.main()
```
</div>

---

## 2. Interactive Testing with `doctest`

The `doctest` module searches docstrings for pieces of text that look like interactive Python sessions, executes them, and verifies that the output matches.

<div class="example-box">
<div class="example-title">Example: Writing Doctests in Docstrings</div>

```python
def multiply(a: int, b: int) -> int:
    """Return product of a and b.

    >>> multiply(4, 5)
    20
    >>> multiply(-2, 3)
    -6
    """
    return a * b

if __name__ == "__main__":
    import doctest
    doctest.testmod()
```
</div>

---

## 3. The `logging` Module

Instead of using `print()` statements (which cannot be easily filtered, redirected, or timestamped in production), use Python's built-in `logging` module.

### Logging Severity Levels

1. `DEBUG`: Detailed diagnostic information.
2. `INFO`: Confirmation that operations are working as expected.
3. `WARNING`: Indication that something unexpected happened, or a future issue.
4. `ERROR`: A serious problem preventing a specific function from executing.
5. `CRITICAL`: A catastrophic failure that may cause the entire program to abort.

<div class="example-box">
<div class="example-title">Example: Configuring and Using Logging</div>

```python
import logging

# Configure root logger format and minimum level
logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s [%(levelname)s] %(name)s: %(message)s",
    datefmt="%Y-%m-%d %H:%M:%S"
)

logger = logging.getLogger("AppLogger")

logger.info("Application starting up...")
logger.warning("Configuration file not specified. Using default settings.")
logger.error("Failed to connect to secondary cache. Falling back to DB.")
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
2026-09-14 13:20:00 [INFO] AppLogger: Application starting up...
2026-09-14 13:20:00 [WARNING] AppLogger: Configuration file not specified. Using default settings.
2026-09-14 13:20:00 [ERROR] AppLogger: Failed to connect to secondary cache. Falling back to DB.
</div>
</div>
