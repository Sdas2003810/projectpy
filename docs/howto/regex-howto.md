# Regular Expressions HOWTO (`re`)

Regular expressions (called REs, regexes, or regex patterns) are a specialized language embedded inside Python and available through the `re` module. They allow you to define patterns to search, validate, extract, and substitute complex text sequences.

---

## Metacharacters Reference

Metacharacters are characters with special syntax meaning in regex patterns:

```
.  ^  $  *  +  ?  {  }  [  ]  \  |  (  )
```

| Metacharacter | Meaning | Example | Matches |
| :--- | :--- | :--- | :--- |
| `.` | Any character except newline (or any with `re.DOTALL`) | `a.c` | `"abc"`, `"a9c"` |
| `^` | Start of string (or line with `re.MULTILINE`) | `^Python` | `"Python 3"` |
| `$` | End of string (or line with `re.MULTILINE`) | `done$` | `"well done"` |
| `*` | 0 or more occurrences (greedy) | `ab*` | `"a"`, `"ab"`, `"abbb"` |
| `+` | 1 or more occurrences (greedy) | `ab+` | `"ab"`, `"abbb"` (NOT `"a"`) |
| `?` | 0 or 1 occurrence (greedy) | `https?` | `"http"`, `"https"` |
| `{m,n}` | Between $m$ and $n$ repetitions | `\d{2,4}` | `"12"`, `"123"`, `"1234"` |
| `[]` | Character class (any single character in set) | `[aeiou]` | Any vowel |
| `[^]` | Negated character class | `[^0-9]` | Any non-digit |
| `\|` | Alternation (OR) | `cat\|dog` | `"cat"`, `"dog"` |
| `()` | Capturing group | `(\d{3})-(\d{4})`| Groups area & number |

---

## Predefined Character Classes

Always write regex patterns using **raw strings (`r"..."`)** to prevent Python's string escaping from conflicting with regex escape sequences:

| Class | Equivalent To | Matches |
| :--- | :--- | :--- |
| `\d` | `[0-9]` | Any decimal digit |
| `\D` | `[^0-9]` | Any non-digit character |
| `\s` | `[ \t\n\r\f\v]` | Any whitespace character |
| `\S` | `[^ \t\n\r\f\v]` | Any non-whitespace character |
| `\w` | `[a-zA-Z0-9_]` (ASCII) or Unicode alphanumeric | Any word character |
| `\W` | `[^\w]` | Any non-word character |
| `\b` | Word boundary anchor | Zero-width boundary between `\w` and `\W` |

---

## Compiling Patterns with `re.compile()`

When performing repetitive searches inside loops, compile the regex once into a reusable `Pattern` object for maximum performance:

```python
import re

# Compile once
email_pattern = re.compile(r"^[\w\.-]+@[\w\.-]+\.\w+$")

# Reuse across multiple inputs
emails = ["alice@example.com", "invalid-email@", "bob.dev@company.org"]
for email in emails:
    if email_pattern.match(email):
        print(f"Valid: {email}")
```

<div class="terminal-output">
Valid: alice@example.com
Valid: bob.dev@company.org
</div>

---

## Core Matching Functions

| Function | What it does |
| :--- | :--- |
| `match(pattern, str)` | Checks for a match **only at the beginning** of the string |
| `search(pattern, str)` | Scans through the entire string to find the **first match** |
| `findall(pattern, str)`| Returns a list of all matching string segments |
| `finditer(pattern, str)`| Yields an iterator of `Match` objects (memory-efficient for large text) |
| `sub(pattern, repl, str)`| Replaces matching substrings with a replacement |

```python
import re

log_data = "Error at 10:15:32. Warning at 10:18:04. Error at 10:22:11."

# search: finds first match
first_error = re.search(r"Error at (\d{2}:\d{2}:\d{2})", log_data)
if first_error:
    print(f"First error timestamp: {first_error.group(1)}")

# findall: extracts all matches as list of strings
all_timestamps = re.findall(r"\d{2}:\d{2}:\d{2}", log_data)
print(f"All timestamps: {all_timestamps}")
```

<div class="terminal-output">
First error timestamp: 10:15:32
All timestamps: ['10:15:32', '10:18:04', '10:22:11']
</div>

---

## Capturing Groups and Named Groups

Parentheses define sub-patterns whose matched text can be extracted independently:

=== "Numbered Groups"
    ```python
    phone_pattern = re.compile(r"(\d{3})-(\d{3})-(\d{4})")
    m = phone_pattern.search("Call me at 415-555-2671 tomorrow.")

    print(m.group(0))  # "415-555-2671" (Full match)
    print(m.group(1))  # "415" (Area code)
    print(m.groups())  # ('415', '555', '2671')
    ```

=== "Named Groups (`?P<name>...`)"
    ```python
    date_pattern = re.compile(r"(?P<year>\d{4})-(?P<month>\d{2})-(?P<day>\d{2})")
    m = date_pattern.search("Release date: 2026-09-14")

    print(m.group("year"))   # "2026"
    print(m.group("month"))  # "09"
    print(m.groupdict())     # {'year': '2026', 'month': '09', 'day': '14'}
    ```

---

## Greedy vs Non-Greedy (Lazy) Matching

By default, repetition operators (`*`, `+`, `{m,n}`) are **greedy**: they consume as much text as possible. Append a question mark (`?`) to make them **non-greedy (lazy)**:

```python
html = "<div>First block</div><div>Second block</div>"

# Greedy: consumes from the first <div> all the way to the LAST </div>!
greedy_match = re.findall(r"<div>.*</div>", html)
print("Greedy count:", len(greedy_match))  # 1 huge match!

# Lazy (Non-greedy): stops at the very first closing </div>
lazy_match = re.findall(r"<div>.*?</div>", html)
print("Lazy count:  ", len(lazy_match))    # 2 separate matches!
```

---

## Readable Multi-Line Regexes with `re.VERBOSE` (`re.X`)

Complex regular expressions quickly turn into unreadable "line noise". The `re.VERBOSE` flag ignores whitespace and allows `#` comments inside your pattern:

```python
import re

phone_regex = re.compile(r"""
    ^                   # Beginning of string
    (\+\d{1,3}\s*)?     # Optional country code (+1, +44, etc.)
    \(?(\d{3})\)?       # 3-digit area code with optional parentheses
    [\s.-]?             # Optional separator (space, dot, dash)
    (\d{3})             # 3-digit exchange prefix
    [\s.-]?             # Optional separator
    (\d{4})             # 4-digit subscriber line
    $                   # End of string
""", re.VERBOSE)

print(bool(phone_regex.match("+1 (415) 555-2671")))  # True
print(bool(phone_regex.match("415.555.2671")))        # True
```
