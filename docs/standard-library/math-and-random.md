# Math, Random, and Statistics Modules

Python provides comprehensive mathematical, statistical, and pseudo-random number capabilities built directly into its standard library.

---

## 1. The `math` Module

The `math` module provides access to standard mathematical functions for floating-point calculations.

### Common `math` Functions

| Function | Description | Example | Result |
| :--- | :--- | :--- | :--- |
| `math.sqrt(x)` | Square root | `math.sqrt(49)` | `7.0` |
| `math.ceil(x)` | Smallest integer greater than or equal to x | `math.ceil(4.2)` | `5` |
| `math.floor(x)`| Largest integer less than or equal to x | `math.floor(4.8)` | `4` |
| `math.gcd(a, b)`| Greatest Common Divisor | `math.gcd(24, 36)` | `12` |
| `math.lcm(a, b)`| Least Common Multiple | `math.lcm(4, 6)` | `12` |
| `math.comb(n, k)`| Combinations (n choose k) | `math.comb(5, 2)` | `10` |
| `math.perm(n, k)`| Permutations | `math.perm(5, 2)` | `20` |

<div class="example-box">
<div class="example-title">Example: Using math Functions</div>

```python
import math

print("Pi:", math.pi)
print("Euler's Number (e):", math.e)
print("Square Root of 64:", math.sqrt(64))
print("Factorial of 5:", math.factorial(5))
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
Pi: 3.141592653589793
Euler's Number (e): 2.718281828459045
Square Root of 64: 8.0
Factorial of 5: 120
</div>
</div>

---

## 2. The `random` Module

The `random` module provides tools to generate pseudo-random numbers, make random selections, and shuffle data.

### Important Random Functions

| Function | Description | Example |
| :--- | :--- | :--- |
| `random.random()` | Returns float between `0.0` and `1.0` | `random.random()` |
| `random.randint(a, b)` | Returns integer $N$ such that $a \le N \le b$ | `random.randint(1, 10)` |
| `random.choice(seq)` | Selects a single random item from sequence | `random.choice(["A", "B", "C"])` |
| `random.choices(seq, k)` | Picks $k$ items with replacement (duplicates possible) | `random.choices(["A", "B"], k=3)` |
| `random.sample(seq, k)` | Picks $k$ unique items without replacement | `random.sample(range(100), 5)` |
| `random.shuffle(lst)` | Shuffles list in-place | `random.shuffle(cards)` |

<div class="example-box">
<div class="example-title">Example: Generating Random Values</div>

```python
import random

# Roll a six-sided die
die_roll = random.randint(1, 6)
print("Die Roll:", die_roll)

# Pick 3 random winners from a list
participants = ["Alice", "Bob", "Charlie", "David", "Emma"]
winners = random.sample(participants, 3)
print("Winners:", winners)
```
</div>

---

## 3. The `statistics` Module

The `statistics` module provides mathematical statistics functions for analyzing numeric data.

| Function | Description |
| :--- | :--- |
| `statistics.mean(data)` | Arithmetic average of data |
| `statistics.median(data)` | Middle value of data |
| `statistics.mode(data)` | Most frequent value in data |
| `statistics.stdev(data)` | Sample standard deviation |
| `statistics.variance(data)` | Sample variance |

<div class="example-box">
<div class="example-title">Example: Computing Summary Statistics</div>

```python
import statistics

grades = [85, 90, 78, 92, 88, 90, 95]

print("Mean:", statistics.mean(grades))
print("Median:", statistics.median(grades))
print("Mode:", statistics.mode(grades))
print("Standard Deviation:", round(statistics.stdev(grades), 2))
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
Mean: 88.28571428571429
Median: 90
Mode: 90
Standard Deviation: 5.56
</div>
</div>
