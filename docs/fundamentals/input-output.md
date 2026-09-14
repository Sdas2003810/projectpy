# Input and Output

Python gives you several ways to get data in and display data out — from the simple `print()` to formatted strings to full file I/O. This chapter covers everything the official docs call "Input and Output".

---

## `print()` — Displaying Output

The most basic output function:

```python
print("Hello, World!")          # Hello, World!
print(42)                       # 42
print(3.14)                     # 3.14
print(True)                     # True
print(None)                     # None
print([1, 2, 3])                # [1, 2, 3]
```

### Multiple Arguments

```python
print("Name:", "Alice", "Age:", 30)   # Name: Alice Age: 30
# items separated by space by default
```

### `sep` and `end`

```python
print("a", "b", "c", sep="-")     # a-b-c
print("a", "b", "c", sep="")      # abc
print("loading", end="...")        # loading...  (no newline)
print("done")                      # done
```

### `file` Parameter

```python
import sys
print("Error occurred", file=sys.stderr)   # writes to stderr

with open("log.txt", "a") as f:
    print("log entry", file=f)            # writes to file
```

---

## f-Strings — Formatted String Literals (Python 3.6+)

F-strings are the modern, preferred way to embed expressions in strings. Prefix with `f` or `F`, then use `{expression}` inside:

```python
name = "Alice"
age = 30
print(f"Hello, {name}! You are {age} years old.")
# Hello, Alice! You are 30 years old.

# Any expression works inside {}
print(f"2 + 2 = {2 + 2}")           # 2 + 2 = 4
print(f"Upper: {name.upper()}")      # Upper: ALICE
print(f"List: {[x**2 for x in range(5)]}")  # List: [0, 1, 4, 9, 16]
```

### Format Specifiers

The format spec goes after a colon inside `{}`:

```python
pi = 3.14159265358979

print(f"{pi:.2f}")     # 3.14   — 2 decimal places
print(f"{pi:.5f}")     # 3.14159
print(f"{pi:10.3f}")   # "     3.142"  — width 10, right-aligned
print(f"{pi:<10.3f}")  # "3.142     "  — left-aligned
print(f"{pi:^10.3f}")  # "  3.142   "  — centered

n = 1000000
print(f"{n:,}")        # 1,000,000  — thousands separator
print(f"{n:_}")        # 1_000_000  — underscore separator
print(f"{n:e}")        # 1.000000e+06

# Integers
x = 255
print(f"{x:d}")        # 255    — decimal
print(f"{x:b}")        # 11111111 — binary
print(f"{x:o}")        # 377    — octal
print(f"{x:x}")        # ff     — hex lowercase
print(f"{x:X}")        # FF     — hex uppercase
print(f"{x:#x}")       # 0xff   — with prefix
```

### Padding and Alignment

```python
name = "Alice"
score = 95.5

print(f"{name:<10} {score:>8.1f}")  # "Alice       95.5"
print(f"{'='*20}")                   # ====================

# Useful for tabular output:
students = [("Alice", 95), ("Bob", 87), ("Carol", 91)]
print(f"{'Name':<10} {'Score':>6}")
print("-" * 18)
for n, s in students:
    print(f"{n:<10} {s:>6}")
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">Name       Score
------------------
Alice          95
Bob            87
Carol          91</div>

### Debug Mode with `=`

```python
x = 42
print(f"{x=}")       # x=42  — prints name and value (Python 3.8+)
print(f"{x*2=}")     # x*2=84
```

---

## `str.format()` — The Older Way

Before f-strings, `str.format()` was standard. You'll see it in older codebases:

```python
name = "Bob"
age = 25
print("Hello, {}! You are {} years old.".format(name, age))
# Hello, Bob! You are 25 years old.

# Named placeholders
print("Hello, {name}! Age: {age}".format(name="Carol", age=40))

# Positional
print("{0} and {1} and {0}".format("spam", "eggs"))
# spam and eggs and spam

# Format spec
print("{:.2f}".format(3.14159))  # 3.14
print("{:>10}".format("right"))  # "     right"
```

---

## `%` Formatting — The Oldest Way

The oldest style, inherited from C's `printf`. Avoid in new code, but you'll see it in legacy codebases:

```python
print("Hello, %s! You are %d years old." % ("Dave", 35))
print("Pi is approximately %.4f" % 3.14159)
```

---

## `input()` — Reading User Input

`input()` reads a line from standard input and returns it as a **string**:

```python
name = input("What's your name? ")
print(f"Hello, {name}!")

# input always returns str — convert explicitly
age_str = input("Enter your age: ")
age = int(age_str)   # Convert to int
print(f"Next year you'll be {age + 1}")

# One-liner
age = int(input("Age: "))
```

!!! warning "`input()` always returns a string"
    If you type `42` at the prompt, `input()` gives you the string `"42"`, not the integer `42`. Always convert explicitly with `int()`, `float()`, etc.

```python
# This crashes if user types non-numeric input
try:
    n = int(input("Enter a number: "))
    print(f"Double: {n * 2}")
except ValueError:
    print("That's not a number!")
```

---

## File I/O — Reading and Writing Files

### Opening Files

```python
# Open for reading (default mode)
f = open("data.txt", "r")

# Open for writing (creates or overwrites)
f = open("output.txt", "w")

# Open for appending
f = open("log.txt", "a")

# Open for reading and writing
f = open("data.txt", "r+")

# Binary mode
f = open("image.png", "rb")
```

**Always use `with` to open files** — it guarantees the file is closed even if an error occurs:

```python
with open("data.txt", "r") as f:
    content = f.read()
# File is automatically closed here
```

### Reading Files

```python
# Read entire file as one string
with open("data.txt") as f:
    content = f.read()
    print(content)

# Read line by line (memory efficient for large files)
with open("data.txt") as f:
    for line in f:
        print(line.strip())   # strip() removes the trailing newline

# Read all lines into a list
with open("data.txt") as f:
    lines = f.readlines()    # ['line1\n', 'line2\n', ...]

# Read one line at a time
with open("data.txt") as f:
    first_line = f.readline()
    second_line = f.readline()
```

### Writing Files

```python
# Write a string
with open("output.txt", "w") as f:
    f.write("Hello, file!\n")
    f.write("Second line\n")

# writelines() writes a list of strings (no newlines added automatically)
lines = ["line1\n", "line2\n", "line3\n"]
with open("output.txt", "w") as f:
    f.writelines(lines)

# print() to a file
with open("output.txt", "w") as f:
    print("Hello", file=f)
    print("World", file=f)
```

### Encoding

Always specify encoding for text files to avoid platform-specific behavior:

```python
# UTF-8 is the standard choice
with open("data.txt", "r", encoding="utf-8") as f:
    content = f.read()

with open("output.txt", "w", encoding="utf-8") as f:
    f.write("Unicode: 你好, мир, مرحبا\n")
```

### File Positions

```python
with open("data.txt", "r") as f:
    print(f.tell())        # current position in bytes
    chunk = f.read(10)     # read 10 bytes
    print(f.tell())        # position moved forward 10

    f.seek(0)              # go back to start
    f.seek(0, 2)           # seek to end (2 = SEEK_END)
    size = f.tell()        # file size in bytes
```

---

## `pathlib` — Modern File Paths

For anything beyond simple `open()`, use `pathlib.Path`:

```python
from pathlib import Path

# Create a path object
p = Path("data") / "subdir" / "file.txt"
print(p)               # data/subdir/file.txt

# Check existence
p.exists()             # True or False
p.is_file()
p.is_dir()

# Read/write directly
text = p.read_text(encoding="utf-8")
p.write_text("new content", encoding="utf-8")

# File metadata
p.stat().st_size       # file size in bytes
p.name                 # "file.txt"
p.stem                 # "file"
p.suffix               # ".txt"
p.parent               # Path("data/subdir")
```

See the full [pathlib guide](../standard-library/pathlib.md).

---

## `json` — Structured Data

For structured data, JSON is the standard interchange format:

```python
import json

# Python dict → JSON string
data = {"name": "Alice", "age": 30, "scores": [95, 87, 91]}
json_str = json.dumps(data)
print(json_str)
# {"name": "Alice", "age": 30, "scores": [95, 87, 91]}

# Pretty-print
print(json.dumps(data, indent=2))

# JSON string → Python dict
parsed = json.loads(json_str)
print(parsed["name"])  # Alice

# Read/write JSON files
with open("data.json", "w") as f:
    json.dump(data, f, indent=2)

with open("data.json") as f:
    loaded = json.load(f)
```
