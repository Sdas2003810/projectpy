# `warnings` — Warning Control & Deprecations

While exceptions signal unrecoverable or unexpected program failures and halt execution, **warnings** inform programmers and users about sub-optimal conditions without interrupting program flow:
- Anticipated API deprecations before a breaking change is released.
- Potential programming mistakes that are syntactically valid but likely unintended.
- Inefficient configurations or runtime states.

The standard library `warnings` module provides fine-grained control over how warnings are emitted, filtered, and caught.

---

## Warning Categories Hierarchy

All warnings inherit from the built-in `Warning` class (which itself inherits from `Exception`):

```
Exception
 └── Warning
      ├── UserWarning (default for warnings.warn)
      ├── DeprecationWarning (features deprecated for developers)
      ├── FutureWarning (features changing behavior for end-users)
      ├── PendingDeprecationWarning (early warning of future deprecation)
      ├── SyntaxWarning (dubious syntax constructs)
      ├── RuntimeWarning (dubious runtime behaviors, e.g. unclosed files/coroutines)
      ├── ResourceWarning (unclosed sockets, files, or subprocesses)
      ├── ImportWarning (probable mistakes in module imports)
      ├── UnicodeWarning (unicode and string conversion ambiguities)
      └── EncodingWarning (implicit encoding used in open() - Python 3.10+)
```

---

## Emitting Warnings with `warnings.warn`

To alert users of an upcoming API change in your library or application, use `warnings.warn()`:

```python
import warnings

def fetch_user_v1(user_id: int):
    warnings.warn(
        "fetch_user_v1() is deprecated and will be removed in v3.0. Use fetch_user() instead.",
        category=DeprecationWarning,
        stacklevel=2
    )
    # Fallback legacy implementation
    return {"id": user_id, "name": "Legacy User"}
```

### The Crucial Role of `stacklevel=2`

By default (`stacklevel=1`), Python attributes the warning to the line inside the library where `warnings.warn` was called:
<div class="terminal-output">
library.py:5: DeprecationWarning: fetch_user_v1() is deprecated
</div>

Setting `stacklevel=2` instructs Python to inspect the call stack and attribute the warning to the **caller's code** that invoked the deprecated function:
<div class="terminal-output">
main.py:42: DeprecationWarning: fetch_user_v1() is deprecated
</div>
This immediately directs developers to the exact line in their own code that requires migration.

---

## Controlling Warnings via Filters

You can configure Python's warning behavior globally or per-category using `filterwarnings()`:

```python
import warnings

# Action values:
# 'error'   : Turn matching warnings into exceptions
# 'ignore'  : Silently drop matching warnings
# 'always'  : Always print matching warnings
# 'default' : Print the first occurrence from each location
# 'module'  : Print the first occurrence from each module
# 'once'    : Print only once per process

# Ignore all DeprecationWarnings
warnings.simplefilter("ignore", DeprecationWarning)

# Turn ResourceWarnings into hard errors
warnings.filterwarnings("error", category=ResourceWarning)
```

---

## Command-Line Warning Switches (`-W`)

You can control warning filters without modifying Python code using the `-W` switch:

```bash
# Turn all warnings into fatal errors (excellent for CI/CD test pipelines)
python -W error my_script.py

# Ignore DeprecationWarning specifically
python -W ignore::DeprecationWarning my_script.py

# Fail if any ResourceWarning (unclosed files) is triggered
python -W error::ResourceWarning my_script.py
```

---

## Testing Warnings with `catch_warnings`

When writing unit tests for your library, you should verify that deprecated functions emit appropriate warnings:

```python
import warnings
import unittest

def legacy_square(x: int) -> int:
    warnings.warn("legacy_square is deprecated", DeprecationWarning, stacklevel=2)
    return x * x

class TestWarnings(unittest.TestCase):
    def test_deprecation_warning_emitted(self):
        with warnings.catch_warnings(record=True) as recorded:
            # Enable all warnings within this context block
            warnings.simplefilter("always")
            
            result = legacy_square(4)
            
            self.assertEqual(result, 16)
            self.assertEqual(len(recorded), 1)
            self.assertTrue(issubclass(recorded[-1].category, DeprecationWarning))
            self.assertIn("legacy_square is deprecated", str(recorded[-1].message))
```

---

## Summary Best Practices

1. **Always provide migration guidance**: In deprecation warnings, tell the developer what to use instead and in which version the feature will be removed.
2. **Use `stacklevel=2`**: Never leave `stacklevel` at default for helper functions or library APIs.
3. **Run tests with `-W error`**: Catch deprecations, unclosed file descriptors, and resource leaks during automated CI builds before they reach production.
