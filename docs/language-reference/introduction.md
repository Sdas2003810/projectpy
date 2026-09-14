# The Python Language Reference

The Language Reference describes the exact syntax and semantics of the Python programming language. Unlike the Tutorial (which teaches you *how to use* Python), this section tells you *exactly what Python does* — useful when you hit a corner case that seems like a bug but isn't, or when you're building tools that work with Python code.

---

## What's in This Section

| Chapter | What it covers |
|---------|---------------|
| [Lexical Analysis](lexical-analysis.md) | How Python reads source code: encoding, identifiers, keywords, literals, operators |
| [Data Model](data-model.md) | Objects, types, values, the object hierarchy, and all special methods |
| [Execution Model](execution-model.md) | Names, namespaces, scope, the LEGB rule, code blocks |
| [The Import System](import-system.md) | How `import` works, finders, loaders, `sys.path`, `__init__.py` |
| [Expressions](expressions.md) | Every kind of expression: atoms, calls, subscripts, conditionals, lambdas |
| [Simple Statements](statements.md) | `assert`, `del`, `return`, `yield`, `raise`, `import`, `global`, `nonlocal` |
| [Compound Statements](compound-statements.md) | `if`, `while`, `for`, `try`, `with`, `match`, function and class definitions |
| [Top-Level Components](top-level-components.md) | Complete programs, file input, interactive input, expression input |
| [Full Grammar](full-grammar.md) | The formal BNF grammar of the entire language |

---

## Who Should Read This

- **Beginners**: You don't need this section. The Tutorial covers everything you need to build things.
- **Intermediate developers**: Skim the Data Model and Execution Model chapters when you hit a confusing scoping or object behavior question.
- **Library/framework authors**: Essential reading — especially the Data Model (dunder methods) and Import System.
- **Tool builders** (linters, formatters, IDEs, debuggers): The full grammar and lexical analysis sections are where you start.

---

## Key Concepts Explained

### Everything is an Object

In Python, *everything* is an object. Integers are objects. Functions are objects. Classes are objects. Even `None` is an object (a singleton of type `NoneType`). Every object has:

- An **identity** (returned by `id()` — CPython uses the memory address)
- A **type** (returned by `type()`)
- A **value** (its content)

```python
x = 42
print(type(x))   # <class 'int'>
print(id(x))     # some memory address (CPython)

def greet(): pass
print(type(greet))  # <class 'function'>
print(id(greet))    # functions are objects too
```

### Mutable vs Immutable

Objects are either mutable or immutable:

| Mutable | Immutable |
|---------|-----------|
| `list` | `int`, `float`, `complex` |
| `dict` | `str` |
| `set` | `tuple` |
| `bytearray` | `bytes`, `frozenset` |
| User-defined classes (usually) | `bool`, `NoneType` |

Immutable objects can't be changed after creation. When you "modify" a string, Python creates a new string object.

### The Name Binding Model

Python doesn't have "variables" in the C sense. It has **names** that are bound to objects. Assignment (`=`) binds a name to an object:

```python
x = [1, 2, 3]  # name 'x' is bound to a list object
y = x           # name 'y' is bound to the SAME list object
y.append(4)
print(x)        # [1, 2, 3, 4] — same object
```

This is why "mutable default argument" bugs happen, and why you need to understand reference semantics when passing objects to functions.
