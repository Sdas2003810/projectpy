# Numbers and Arithmetic Operations

Python supports integers, floating-point numbers, and complex numbers out of the box.

---

## Numeric Types in Python

1. **Integer (`int`)**: Whole numbers without decimals. Python integers have unlimited precision.
2. **Floating-point (`float`)**: Numbers containing a decimal point, implemented using IEEE 754 double precision.
3. **Complex (`complex`)**: Numbers written in the form $a + bj$, where $j$ is the imaginary unit.

<div class="example-box">
<div class="example-title">Example: Numeric Types</div>

```python
x = 10         # int
pi = 3.14159   # float
z = 2 + 5j     # complex

print(type(x))
print(type(pi))
print(type(z))
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
&lt;class 'int'&gt;
&lt;class 'float'&gt;
&lt;class 'complex'&gt;
</div>
</div>

---

## Arithmetic Operators

| Operator | Operation | Syntax | Example | Result |
| :--- | :--- | :--- | :--- | :--- |
| `+` | Addition | `a + b` | `10 + 5` | `15` |
| `-` | Subtraction | `a - b` | `10 - 3` | `7` |
| `*` | Multiplication | `a * b` | `4 * 3` | `12` |
| `/` | True Division | `a / b` | `7 / 2` | `3.5` (always float) |
| `//` | Floor Division | `a // b` | `7 // 2` | `3` (discards fractional part) |
| `%` | Modulo | `a % b` | `7 % 2` | `1` (remainder) |
| `**` | Exponentiation | `a ** b` | `2 ** 3` | `8` |

---

## Division: True Division vs Floor Division

- True division (`/`) always returns a floating-point number, even if the division divides evenly.
- Floor division (`//`) rounds down to the nearest integer.

<div class="example-box">
<div class="example-title">Example: Division Comparison</div>

```python
print(8 / 4)    # Output: 2.0 (float)
print(8 // 4)   # Output: 2 (int)
print(17 // 3)  # Output: 5
print(17 % 3)   # Output: 2 (remainder)
```
</div>

---

## Exponentiation and Large Numbers

Use `**` to calculate powers:

<div class="example-box">
<div class="example-title">Example: Powers and Large Integers</div>

```python
print(2 ** 8)    # 256
print(2 ** 100)  # 1267650600228229401496703205376
```
</div>

In Python 3, integers automatically handle arbitrarily large numbers without buffer overflow.

---

## Underscores in Numeric Literals

To improve readability of large numbers, underscores can be placed between digits:

```python
population = 1_400_000_000
price = 10_000.50
```

---

## Augmented Assignment Operators

Operators can be combined with `=` to modify a variable in-place:

```python
count = 10
count += 5   # Equivalent to: count = count + 5 (15)
count -= 2   # Equivalent to: count = count - 2 (13)
count *= 2   # Equivalent to: count = count * 2 (26)
count /= 2   # Equivalent to: count = count / 2 (13.0)
```
