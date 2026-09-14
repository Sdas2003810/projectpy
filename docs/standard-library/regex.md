# Regular Expressions (`re` Module)

A **Regular Expression** (RegEx) is a special sequence of characters that helps you match, locate, or replace patterns in text strings.

In Python, the `re` module provides full support for regular expressions.

---

## 1. Common `re` Functions

| Function | Description |
| :--- | :--- |
| `re.search(pattern, string)` | Scans through string looking for the first location where pattern matches |
| `re.match(pattern, string)` | Checks for a match ONLY at the beginning of the string |
| `re.findall(pattern, string)`| Returns a list of all non-overlapping matches in the string |
| `re.finditer(pattern, string)`| Returns an iterator yielding Match objects over all matches |
| `re.sub(pattern, repl, string)`| Replaces occurrences of pattern with `repl` |
| `re.split(pattern, string)` | Splits string by occurrences of the pattern |

---

## 2. Metacharacters and Syntax

| Character | Description | Example | Matches |
| :--- | :--- | :--- | :--- |
| `.` | Any character except newline | `c.t` | `cat`, `cot`, `c9t` |
| `^` | Starts with | `^Hello` | Strings starting with `Hello` |
| `$` | Ends with | `world$` | Strings ending with `world` |
| `*` | Zero or more occurrences | `ab*` | `a`, `ab`, `abbb` |
| `+` | One or more occurrences | `ab+` | `ab`, `abbb` (not `a`) |
| `?` | Zero or one occurrence | `ab?` | `a` or `ab` |
| `{n,m}`| Between n and m occurrences | `a{2,4}` | `aa`, `aaa`, `aaaa` |
| `[]` | A set of characters | `[a-z]` | Any lowercase letter |
| `|` | Either or | `cat|dog` | `cat` or `dog` |
| `()` | Capture and group | `(ab)+` | `ab`, `abab` |

### Special Sequences

| Sequence | Matches | Equivalent To |
| :--- | :--- | :--- |
| `\d` | Any decimal digit | `[0-9]` |
| `\D` | Any non-digit character | `[^0-9]` |
| `\w` | Any alphanumeric character or underscore | `[a-zA-Z0-9_]` |
| `\W` | Any non-alphanumeric character | `[^a-zA-Z0-9_]` |
| `\s` | Any whitespace character (space, tab, newline) | `[ \t\n\r\f\v]` |
| `\S` | Any non-whitespace character | `[^ \t\n\r\f\v]` |

---

## 3. Practical Examples

### Finding All Email Addresses

<div class="example-box">
<div class="example-title">Example: Extracting Emails with re.findall()</div>

```python
import re

text = "Contact support@projectpy.dev or sales-team@company.org for assistance."

# Pattern: username@domain.extension
pattern = r"[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}"

emails = re.findall(pattern, text)
print(emails)
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
['support@projectpy.dev', 'sales-team@company.org']
</div>
</div>

---

### Validating Phone Numbers with Capturing Groups

<div class="example-box">
<div class="example-title">Example: Parsing Groups with re.search()</div>

```python
import re

phone_text = "Call us at 555-123-4567 today."
pattern = r"(\d{3})-(\d{3})-(\d{4})"

match = re.search(pattern, phone_text)
if match:
    print("Full Match: ", match.group(0))
    print("Area Code:  ", match.group(1))
    print("Prefix:     ", match.group(2))
    print("Line Number:", match.group(3))
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
Full Match:  555-123-4567
Area Code:   555
Prefix:      123
Line Number: 4567
</div>
</div>

---

### Search and Replace with `re.sub()`

<div class="example-box">
<div class="example-title">Example: Masking Sensitive Data</div>

```python
import re

text = "Customer SSN: 123-45-6789 and 987-65-4321."
# Replace first 5 digits with asterisks
masked = re.sub(r"\d{3}-\d{2}-", "***-**-", text)

print(masked)
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
Customer SSN: ***-**-6789 and ***-**-4321.
</div>
</div>
