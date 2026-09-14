# Floating Point Arithmetic: Issues and Limitations

Floating-point numbers in Python (represented by the `float` type) are stored internally in binary format (base 2) using IEEE 754 double-precision representation.

Because computers store fractions using powers of 2 rather than powers of 10, certain decimal fractions cannot be represented exactly.

---

## The Precision Problem

Consider the following common test in Python:

<div class="example-box">
<div class="example-title">Example: Adding 0.1 and 0.2</div>

```python
result = 0.1 + 0.2
print(result)
print(result == 0.3)
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
0.30000000000000004
False
</div>
</div>

### Why Does This Happen?

In decimal (base 10), the fraction $1/3$ cannot be written as a finite decimal; it repeats endlessly as $0.333333...$.

Similarly, in binary (base 2), the fraction $1/10$ ($0.1$ in decimal) cannot be written as a finite binary fraction; it repeats endlessly:

```text
0.00011001100110011001100110011...
```

Because a computer's processor has a finite number of bits (53 bits of precision for IEEE 754 doubles), it must truncate this value. The resulting stored approximation is slightly different from the exact mathematical fraction.

---

## How to Compare Floats Safely

Never compare floating-point numbers using the `==` operator. Instead, use Python's built-in `math.isclose()` function:

### Syntax
```python
math.isclose(a, b, rel_tol=1e-09, abs_tol=0.0)
```

<div class="example-box">
<div class="example-title">Example: Comparing Floats with math.isclose()</div>

```python
import math

a = 0.1 + 0.2
b = 0.3

# Correct comparison
print(math.isclose(a, b))
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
True
</div>
</div>

---

## Rounding in Python: Banker's Rounding

Python's built-in `round()` function implements **round half to even** (also called Banker's Rounding).

If a number is exactly halfway between two integers, it rounds to the nearest **even** number:

<div class="example-box">
<div class="example-title">Example: Rounding Behavior</div>

```python
print(round(2.5))  # Rounds to 2 (nearest even number)
print(round(3.5))  # Rounds to 4 (nearest even number)
print(round(4.5))  # Rounds to 4 (nearest even number)
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
2
4
4
</div>
</div>

This strategy minimizes cumulative statistical rounding bias when summing large arrays of numbers.

---

## Exact Calculations: The `decimal` Module

For accounting, banking, financial systems, or any domain where exact decimal precision is required, Python provides the `decimal` module:

<div class="example-box">
<div class="example-title">Example: Exact Math with decimal.Decimal</div>

```python
from decimal import Decimal

# Always pass strings to Decimal to prevent float conversion error
price1 = Decimal("0.10")
price2 = Decimal("0.20")

total = price1 + price2
print(total)
print(total == Decimal("0.30"))
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
0.30
True
</div>
</div>

---

## Exact Rational Numbers: The `fractions` Module

If you need exact rational arithmetic without decimal conversion, Python provides the `fractions` module:

<div class="example-box">
<div class="example-title">Example: Working with Fractions</div>

```python
from fractions import Fraction

f1 = Fraction(1, 3)
f2 = Fraction(1, 6)

result = f1 + f2
print(result)
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
1/2
</div>
</div>
