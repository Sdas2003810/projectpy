# Lexical Analysis

Lexical analysis is the process of converting source code (a stream of characters) into a sequence of meaningful tokens. Python's lexer reads your `.py` file character by character and produces tokens like "this is a string literal", "this is the `def` keyword", "this is a left parenthesis".

Understanding lexical analysis helps you understand why certain things are or aren't valid Python syntax.

---

## Source Code Encoding

By default, Python source files are **UTF-8**. You can use any UTF-8 character in strings, comments, and identifiers. To declare a different encoding, add a magic comment on line 1 (or line 2 if line 1 is a shebang):

```python
# -*- coding: utf-8 -*-
```

or simply:

```python
# coding: utf-8
```

This is the **coding declaration** (PEP 263). In practice, you almost never need this since UTF-8 is the default.

---

## Line Structure

Python uses newlines to end statements. The **implicit line joining** rules let you continue a statement across multiple lines inside brackets:

```python
# Explicit continuation with backslash (avoid this)
result = 1 + 2 + \
         3 + 4

# Implicit continuation inside brackets (preferred)
result = (
    1 + 2 +
    3 + 4
)

# Also works with [], {}
items = [
    "apple",
    "banana",
    "cherry",
]
```

---

## Indentation

Python uses indentation to define code blocks. The exact number of spaces doesn't matter, but it must be consistent within a block. Conventionally: **4 spaces**.

```python
def example():
    if True:
        x = 1
        if True:
            y = 2  # 8 spaces
```

!!! warning "Tabs vs Spaces"
    Never mix tabs and spaces for indentation. Python 3 raises a `TabError` if it detects mixed indentation. Use spaces exclusively (PEP 8 says 4 spaces).

---

## Comments

Comments start with `#` and extend to end of line. Python has no multi-line comment syntax — use multiple `#` lines or a standalone string literal (though the latter isn't a real comment, it's just an unused expression):

```python
# This is a comment
x = 1  # inline comment

# For multi-line comments:
# Line 1
# Line 2
# Line 3

"""
This is NOT a comment — it's a string literal.
It won't be garbage collected if it's the first statement
in a module, function, or class (it becomes the docstring).
"""
```

---

## Keywords

These words are **reserved** — you can't use them as variable names:

```
False      await      else       import     pass
None       break      except     in         raise
True       class      finally    is         return
and        continue   for        lambda     try
as         def        from       nonlocal   while
assert     del        global     not        with
async      elif       if         or         yield
match      case       type       _
```

!!! note "Soft Keywords"
    `match`, `case`, and `type` are **soft keywords** in Python 3.10+/3.12+. They're only treated as keywords in the right syntactic context — you can still use them as variable names in other contexts: `match = re.match(...)` is valid.

---

## Identifiers

An identifier is any name you define — variables, functions, classes, module names. Rules:

- Must start with a letter (a–z, A–Z) or underscore (`_`)
- Can contain letters, digits (0–9), and underscores
- Case sensitive: `name`, `Name`, and `NAME` are three different identifiers
- Can contain Unicode letters (Python 3)

**Naming conventions (PEP 8):**

| Convention | Used for |
|-----------|---------|
| `lower_case` | Variables, functions, modules |
| `UPPER_CASE` | Constants |
| `CapitalCase` | Classes |
| `_single_leading` | Internal/private (by convention) |
| `__double_leading` | Name mangling (triggers `_ClassName__attr`) |
| `__double_both__` | "Dunder" — special methods |

---

## Literals

### String Literals

```python
# Quotes — single, double, triple
'hello'
"hello"
'''multi
line'''
"""also multi
line"""

# Prefixes
r"raw string — backslashes not processed: \n is literal"
b"bytes literal — not a str"
f"f-string — {1 + 1} is evaluated"
rb"raw bytes"

# Adjacent string literals are concatenated at compile time
message = ("Hello, "
           "World!")  # same as "Hello, World!"
```

### Numeric Literals

```python
# Integer literals
42          # decimal
0b101010    # binary (42 in binary)
0o52        # octal (42 in octal)
0x2A        # hexadecimal (42 in hex)
1_000_000   # underscores for readability (Python 3.6+)

# Float literals
3.14
1.5e-10     # scientific notation: 1.5 × 10⁻¹⁰
.5          # same as 0.5

# Complex literals
3+4j        # real=3, imaginary=4
1j          # same as 0+1j
```

---

## Operators and Delimiters

Python's operators (with full precedence table in the [Operators reference](../reference/operators.md)):

```
+  -  *  **  /  //  %
@  <<  >>  &  |  ^  ~
<  >  <=  >=  ==  !=
:=  =  +=  -=  *=  **=  /=  //=  %=  @=  &=  |=  ^=  <<=  >>=
(  )  [  ]  {  }
,  :  .  ;  @  ->
```

---

## Token Generation Details

Python's lexer produces these token types:

| Token type | Examples |
|-----------|---------|
| `NAME` | `x`, `my_var`, `print` |
| `NUMBER` | `42`, `3.14`, `0xFF` |
| `STRING` | `"hello"`, `b"bytes"` |
| `OP` | `+`, `(`, `[`, `:` |
| `NEWLINE` | end of logical line |
| `INDENT` | increased indentation |
| `DEDENT` | decreased indentation |
| `COMMENT` | `# comment` |
| `ENDMARKER` | end of file |

You can see these tokens using the `tokenize` standard library module:

```python
import tokenize, io

source = 'x = 1 + 2\n'
tokens = list(tokenize.generate_tokens(io.StringIO(source).readline))
for tok in tokens:
    print(tok)
```
