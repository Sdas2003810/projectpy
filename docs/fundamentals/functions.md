# Functions and Arguments

A function is a block of organized, reusable code that is used to perform a single, related action. Functions provide better modularity for your application and a high degree of code reusing.

---

## Defining a Function

Functions are defined using the `def` keyword, followed by the function name and parentheses `()`.

### Syntax
```python
def function_name(parameters):
    """Docstring explaining function purpose."""
    # function body
    return value
```

<div class="example-box">
<div class="example-title">Example: Basic Function Definition</div>

```python
def add_numbers(a: int, b: int) -> int:
    """Return the sum of a and b."""
    return a + b

result = add_numbers(10, 20)
print("Result:", result)
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
Result: 30
</div>
</div>

---

## Positional and Keyword Arguments

- **Positional arguments**: Arguments passed in the exact order declared in the function header.
- **Keyword arguments**: Arguments passed by specifying parameter names (`name=value`).

```python
def describe_pet(animal_type, pet_name):
    print(f"I have a {animal_type} named {pet_name}.")

# Positional
describe_pet("dog", "Buddy")

# Keyword (order does not matter)
describe_pet(pet_name="Whiskers", animal_type="cat")
```

---

## Default Parameter Values

You can provide default values for parameters. If the caller does not supply an argument, the default is used:

```python
def greet(name, greeting="Hello"):
    return f"{greeting}, {name}!"

print(greet("Alice"))            # Hello, Alice!
print(greet("Bob", greeting="Hi")) # Hi, Bob!
```

### Important Warning: Mutable Default Arguments

Default argument values are evaluated **only once** at the time of function definition. If you use a mutable object (like a list or dict) as a default, it will be shared across all calls:

<div class="example-box">
<div class="example-title">Example: The Mutable Default Argument Bug and Fix</div>

```python
# Problematic:
def bad_append(item, target_list=[]):
    target_list.append(item)
    return target_list

print(bad_append(1))  # [1]
print(bad_append(2))  # [1, 2] (Shared across calls!)

# Correct Idiomatic Pattern:
def good_append(item, target_list=None):
    if target_list is None:
        target_list = []
    target_list.append(item)
    return target_list

print(good_append(1))  # [1]
print(good_append(2))  # [2] (Independent)
```
</div>

---

## Special Parameters: Positional-Only (`/`) and Keyword-Only (`*`)

In Python 3.8+, you can control whether arguments must be passed by position, by keyword, or either:

```text
def f(pos1, pos2, /, pos_or_kwd, *, kwd1, kwd2):
      -----------    ----------     ----------
        |                |                 |
        |          Positional or keyword   |
        |                                  - Keyword only
         -- Positional only
```

<div class="example-box">
<div class="example-title">Example: Positional-Only and Keyword-Only</div>

```python
def configure(host, port, /, timeout=30, *, secure=True):
    print(f"{host}:{port} (timeout={timeout}, secure={secure})")

# Valid:
configure("localhost", 8000, 60, secure=False)
configure("localhost", 8000, timeout=60, secure=True)

# Invalid:
# configure(host="localhost", port=8000) # TypeError: host is positional-only
# configure("localhost", 8000, 30, True) # TypeError: secure is keyword-only
```
</div>

---

## Arbitrary Arguments: `*args` and `**kwargs`

- `*args`: Collects extra positional arguments into a **tuple**.
- `**kwargs`: Collects extra keyword arguments into a **dictionary**.

<div class="example-box">
<div class="example-title">Example: Using *args and **kwargs</div>

```python
def display_info(title, *args, **kwargs):
    print("Title: ", title)
    print("Args:  ", args)
    print("Kwargs:", kwargs)

display_info("Server Status", "active", "verified", ip="127.0.0.1", port=8080)
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
Title:  Server Status
Args:   ('active', 'verified')
Kwargs: {'ip': '127.0.0.1', 'port': 8080}
</div>
</div>

---

## Lambda Functions (Anonymous Functions)

A lambda function is a small anonymous function defined using the `lambda` keyword. It can take any number of arguments, but can only have one expression:

### Syntax
```python
lambda arguments: expression
```

```python
# Regular function
def square(x):
    return x * x

# Equivalent lambda
square_lambda = lambda x: x * x

# Frequently used in key functions:
pairs = [(1, "one"), (3, "three"), (2, "two")]
pairs.sort(key=lambda pair: pair[0])
print(pairs)  # [(1, 'one'), (2, 'two'), (3, 'three')]
```
