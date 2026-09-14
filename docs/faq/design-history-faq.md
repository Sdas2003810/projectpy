# Design and History FAQ

Answers to historical design questions explaining why Python was built the way it was, and the principles behind its syntax decisions.

---

## Why does Python use indentation for statement grouping?

Guido van Rossum chose indentation for grouping because in languages with curly braces (like C or Java), programmers indent their code anyway to make it readable for humans.

When indentation and braces diverge (such as a missing brace or misleading indentation), bugs like Apple's famous `goto fail;` security flaw occur. By making indentation syntactically meaningful:
- The visual structure seen by the programmer matches the code structure parsed by the compiler.
- Code style across different teams and codebases remains remarkably consistent.
- Code avoids the clutter of trailing braces and semicolons.

---

## Why are colons required on `if`, `for`, `def`, and `class` statements?

Colons serve two primary purposes:
1. **Human Readability**: The colon clearly separates the condition/header clause from the indented body below it. Research shows that punctuation markers help human eyes scan code blocks faster.
2. **Grammar Parsing**: In one-line statements (e.g. `if x: return 1`), the colon unambiguously separates the predicate from the statement body.

---

## Why are there separate `tuple` and `list` data types?

While both are ordered sequences, their intended semantic roles are distinct:

| Feature | `list` | `tuple` |
| :--- | :--- | :--- |
| **Mutability** | Mutable (can append, remove, reorder) | Immutable (fixed at creation) |
| **Philosophy** | Homogeneous sequence of arbitrary length (e.g. list of user names) | Heterogeneous record with fixed positions (e.g. `(latitude, longitude)`) |
| **Hashability** | Not hashable (cannot be dict keys) | Hashable (if contents are hashable) |
| **Memory** | Over-allocates RAM to support $O(1)$ appends | Exact compact memory layout |

---

## Why `len(x)` instead of `x.len()`?

Guido van Rossum preferred `len(x)` for three reasons:
1. **Readability & Core Protocols**: `len()` is a fundamental mathematical property, consistent with operations like `abs(x)` and `min(x)`.
2. **Polymorphic Built-in Protocol**: `len()` delegates to `x.__len__()`. For built-in types (`str`, `list`, `dict`), CPython reads the size directly from the internal C struct in $O(1)$ time without performing a method lookup.
3. **Uniformity**: It applies uniformly to standard sequences, dictionaries, sets, and custom user classes.

---

## Why is `self` explicit in method definitions?

In Python, methods are simply standard functions defined in a class namespace. When you write:

```python
class Greeter:
    def greet(self, name):
        return f"Hello, {name} from {self}"
```

`greet` is a normal function taking two arguments. When called as `obj.greet("Alice")`, Python's descriptor protocol automatically transforms the call into `Greeter.greet(obj, "Alice")`.

Making `self` explicit:
- Adheres to the Zen of Python: *"Explicit is better than implicit"*.
- Eliminates ambiguity between instance attributes (`self.x`) and local variables (`x`).
- Allows dynamic borrowing and decorating of methods without hidden language magic.

---

## Why doesn't Python have `++` or `--` operators?

In C and Java, `x++` increments `x` and returns its previous value.

In Python:
1. Integers are **immutable**. You cannot change the number `5` in memory; you can only rebind the variable `x` to a new number `6`.
2. Allowing `++x` would be ambiguous because `+` is already the unary plus operator (so `++x` in Python parses as `+(+x)`, returning `x`).
3. Use the explicit augmented assignment instead: `x += 1`.
