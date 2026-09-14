# Strings and Text Manipulation

A string in Python is a sequence of Unicode characters enclosed in single quotes (`'...'`) or double quotes (`"..."`). Strings are immutable, meaning they cannot be modified after creation.

---

## Defining Strings

```python
# Single quotes and double quotes behave identically
str1 = 'Python'
str2 = "Python"

# Escaping special characters with backslash (\)
quote = 'He said, "It\'s raining."'

# Multiline strings using triple quotes
multiline = """Line 1
Line 2
Line 3"""

# Raw strings (prefix r or R) ignore backslash escape sequences
raw_path = r"C:\new_folder\test.txt"
```

---

## String Indexing

Python sequences use **zero-based indexing**. You can access characters from the beginning using non-negative integers ($0, 1, 2, ...$) or from the end using negative integers ($-1, -2, -3, ...$).

Consider the string `"PYTHON"`:

| Character | P | Y | T | H | O | N |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **Positive Index** | `0` | `1` | `2` | `3` | `4` | `5` |
| **Negative Index** | `-6` | `-5` | `-4` | `-3` | `-2` | `-1` |

<div class="example-box">
<div class="example-title">Example: Accessing String Characters</div>

```python
word = "PYTHON"

print(word[0])   # First character: 'P'
print(word[3])   # Fourth character: 'H'
print(word[-1])  # Last character: 'N'
print(word[-2])  # Second to last: 'O'
```
</div>

---

## String Slicing

Slicing allows you to obtain a substring from a string.

### Syntax
```python
string[start:stop:step]
```

- `start`: Inclusive beginning index (defaults to 0).
- `stop`: Exclusive ending index (slice stops before this index).
- `step`: Stride or step count between characters (defaults to 1).

<div class="example-box">
<div class="example-title">Example: Slicing Substrings</div>

```python
s = "ProjectPy"

print(s[0:7])   # 'Project' (from index 0 to index 6)
print(s[:7])    # 'Project' (start defaults to 0)
print(s[7:])    # 'Py'      (slice from index 7 to end)
print(s[-2:])   # 'Py'      (last 2 characters)
print(s[::2])   # 'PoetP'   (every 2nd character)
print(s[::-1])  # 'yPtcejorP' (reverse string)
```
</div>

---

## Strings are Immutable

Attempting to change an individual character by index raises a `TypeError`:

```python
word = "Python"
# word[0] = 'J'  # TypeError: 'str' object does not support item assignment

# Correct approach: Create a new string
new_word = "J" + word[1:]
print(new_word)  # 'Jython'
```

---

## Formatted String Literals (f-strings)

Formatted string literals (f-strings) provide a concise way to embed expressions inside string literals:

<div class="example-box">
<div class="example-title">Example: Using f-strings</div>

```python
name = "Ada"
score = 98.456

# Variable interpolation
print(f"Student: {name}")

# Number formatting
print(f"Final Score: {score:.2f}")

# Inline expressions
print(f"Double Score: {score * 2:.1f}")
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
Student: Ada
Final Score: 98.46
Double Score: 196.9
</div>
</div>

---

## Common String Methods

| Method | Description | Example | Result |
| :--- | :--- | :--- | :--- |
| `s.lower()` | Returns string in lowercase | `"ABC".lower()` | `'abc'` |
| `s.upper()` | Returns string in uppercase | `"abc".upper()` | `'ABC'` |
| `s.strip()` | Removes leading and trailing whitespace | `" hi ".strip()` | `'hi'` |
| `s.replace(old, new)` | Replaces occurrences of substring | `"cat".replace("c", "b")` | `'bat'` |
| `s.split(sep)` | Splits string into a list by separator | `"a,b,c".split(",")` | `['a', 'b', 'c']` |
| `s.join(iterable)` | Joins iterable of strings using s as separator | `"-".join(['a', 'b'])` | `'a-b'` |
| `s.startswith(prefix)`| Checks if string starts with prefix | `"data.csv".startswith("data")`| `True` |
| `s.endswith(suffix)` | Checks if string ends with suffix | `"data.csv".endswith(".csv")` | `True` |
| `s.find(sub)` | Returns lowest index of substring (or -1) | `"hello".find("l")` | `2` |
| `len(s)` | Returns total number of characters | `len("Python")` | `6` |
