# `string` — Common String Operations & Constants

While Python strings (`str`) have dozens of powerful built-in methods like `.split()`, `.join()`, and `.strip()`, the standard library `string` module provides essential string constants, customizable formatting engines, and safe templating mechanisms.

---

## Standard Character Constants

The `string` module defines pre-computed ASCII collections commonly needed for data validation, token generation, and parsing:

| Constant | Value | Description |
| :--- | :--- | :--- |
| `string.ascii_lowercase` | `'abcdefghijklmnopqrstuvwxyz'` | All 26 lowercase ASCII letters |
| `string.ascii_uppercase` | `'ABCDEFGHIJKLMNOPQRSTUVWXYZ'` | All 26 uppercase ASCII letters |
| `string.ascii_letters` | `ascii_lowercase + ascii_uppercase` | All 52 ASCII letters |
| `string.digits` | `'0123456789'` | Decimal digits |
| `string.hexdigits` | `'0123456789abcdefABCDEF'` | Valid hexadecimal characters |
| `string.octdigits` | `'01234567'` | Valid octal characters |
| `string.punctuation` | `!"#$%&\'()*+,-./:;<=>?@[\\]^_`{\|}~` | Standard ASCII punctuation characters |
| `string.whitespace` | `' \t\n\r\x0b\x0c'` | Space, tab, newline, return, formfeed |
| `string.printable` | `digits + letters + punctuation + whitespace`| All printable ASCII characters |

### Practical Recipe: Generating Cryptographically Secure Passwords

Using `string` constants alongside the `secrets` module ensures unbiased and secure random tokens:

```python
import string
import secrets

def generate_secure_password(length: int = 16) -> str:
    alphabet = string.ascii_letters + string.digits + "!@#$%^&*"
    # Ensure at least one lowercase, uppercase, digit, and symbol
    password = [
        secrets.choice(string.ascii_lowercase),
        secrets.choice(string.ascii_uppercase),
        secrets.choice(string.digits),
        secrets.choice("!@#$%^&*")
    ]
    # Fill remaining characters
    password += [secrets.choice(alphabet) for _ in range(length - 4)]
    
    # Shuffle cryptographically
    secrets.SystemRandom().shuffle(password)
    return "".join(password)

print(generate_secure_password(16))
```

---

## Safe User Templating with `string.Template`

When you allow users to supply message templates (for email notifications, user bios, or customizable report headers), using Python's standard `f-strings` or `.format()` exposes internal variables and object attributes to inspection (e.g. `{user.__init__.__globals__}`).

`string.Template` uses a safe, restricted `$`-based substitution syntax:

```python
from string import Template

# Template with placeholder variables
email_template = Template("Hello $name, your order #$order_id has shipped!")

# 1. Standard substitution: raises KeyError if a placeholder is missing
rendered = email_template.substitute(name="Alice", order_id=45102)
print(rendered)

# 2. Safe substitution: leaves missing placeholders intact rather than raising an exception
partial_template = Template("User: $username (Plan: $plan, Region: $region)")
safe_rendered = partial_template.safe_substitute(username="bob99")
print(safe_rendered)
```

<div class="terminal-output">
Hello Alice, your order #45102 has shipped!
User: bob99 (Plan: $plan, Region: $region)
</div>

### Delimiters in `Template`

- Use `${var}` when the variable is followed immediately by characters without space: `Template("${count}items").substitute(count=5)` -> `'5items'`.
- Use `$$` to escape a literal dollar sign: `Template("Cost: $$$amount").substitute(amount=99)` -> `'Cost: $99'`.

---

## Helper Function: `string.capwords()`

`string.capwords(s, sep=None)` splits the string into words using `s.split(sep)`, capitalizes each word with `.capitalize()`, and joins them back with `sep`:

```python
import string

raw = "the quick   brown   fox jumps"
print(string.capwords(raw))
# "The Quick Brown Fox Jumps"
```

Unlike `str.title()`, which capitalizes letters after punctuation (e.g., `"they're"` becomes `"They'Re"`), `string.capwords()` only capitalizes word boundaries separated by whitespace.

---

## Custom Formatters with `string.Formatter`

The `string.Formatter` class allows you to subclass and extend Python's `.format()` parsing engine:

```python
from string import Formatter

class DefaultDictFormatter(Formatter):
    """A formatter that uses a fallback default for missing keys instead of raising KeyError."""
    def __init__(self, default="[MISSING]"):
        self.default = default

    def get_value(self, key, args, kwargs):
        if isinstance(key, str):
            return kwargs.get(key, self.default)
        return super().get_value(key, args, kwargs)

fmt = DefaultDictFormatter()
result = fmt.format("Hello {name}, your role is {role}.", name="David")
print(result)
```

<div class="terminal-output">
Hello David, your role is [MISSING].
</div>
