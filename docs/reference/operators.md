# Python Operators and Precedence Reference

Operators are special symbols used to perform operations on variables and values. Below is the complete reference of all Python operators and their exact precedence order.

---

## 1. Operator Categories

### Arithmetic Operators

| Operator | Name | Example | Result |
| :--- | :--- | :--- | :--- |
| `+` | Addition | `5 + 3` | `8` |
| `-` | Subtraction | `5 - 3` | `2` |
| `*` | Multiplication | `5 * 3` | `15` |
| `/` | True Division | `5 / 2` | `2.5` |
| `//` | Floor Division | `5 // 2` | `2` |
| `%` | Modulus (Remainder) | `5 % 2` | `1` |
| `**` | Exponentiation | `5 ** 2` | `25` |

---

### Comparison Operators

| Operator | Name | Example | Evaluates To |
| :--- | :--- | :--- | :--- |
| `==` | Equal | `5 == 5` | `True` |
| `!=` | Not equal | `5 != 3` | `True` |
| `>` | Greater than | `5 > 3` | `True` |
| `<` | Less than | `5 < 3` | `False` |
| `>=` | Greater than or equal to | `5 >= 5` | `True` |
| `<=` | Less than or equal to | `3 <= 5` | `True` |

---

### Logical Operators

| Operator | Description | Example |
| :--- | :--- | :--- |
| `and` | Returns `True` if both statements are true | `x > 3 and x < 10` |
| `or` | Returns `True` if one of the statements is true | `x > 3 or x < 4` |
| `not` | Reverses the result, returns `False` if the result is true | `not(x > 3 and x < 10)` |

---

### Identity and Membership Operators

| Operator | Description | Example |
| :--- | :--- | :--- |
| `is` | Returns `True` if both variables are the exact same object in RAM | `x is y` |
| `is not` | Returns `True` if both variables are not the same object | `x is not y` |
| `in` | Returns `True` if a sequence with the specified value is present | `5 in [1, 2, 5]` |
| `not in` | Returns `True` if a sequence with the specified value is not present | `10 not in [1, 2, 5]` |

---

### Bitwise Operators

| Operator | Name | Description |
| :--- | :--- | :--- |
| `&` | AND | Sets each bit to 1 if both bits are 1 |
| `|` | OR | Sets each bit to 1 if one of two bits is 1 |
| `^` | XOR | Sets each bit to 1 if only one of two bits is 1 |
| `~` | NOT | Inverts all the bits |
| `<<` | Zero fill left shift | Shift left by pushing zeros in from the right |
| `>>` | Signed right shift | Shift right by pushing copies of the leftmost bit |

---

### Assignment Operators

| Operator | Example | Equivalent To |
| :--- | :--- | :--- |
| `=` | `x = 5` | `x = 5` |
| `+=` | `x += 3` | `x = x + 3` |
| `-=` | `x -= 3` | `x = x - 3` |
| `*=` | `x *= 3` | `x = x * 3` |
| `/=` | `x /= 3` | `x = x / 3` |
| `//=`| `x //= 3` | `x = x // 3` |
| `%=` | `x %= 3` | `x = x % 3` |
| `**=`| `x **= 3` | `x = x ** 3` |
| `:=` | `if (n := len(a)) > 10:` | Assignment expression (Walrus operator, Python 3.8+) |

---

## 2. Operator Precedence Table

The following table lists Python operators from highest precedence (evaluated first) to lowest precedence (evaluated last).

| Precedence | Operator | Description |
| :--- | :--- | :--- |
| **1 (Highest)** | `(expressions...)`, `[expressions...]`, `{key: value...}` | Binding or parenthesized expression, list display, dictionary display, set display |
| **2** | `x[index]`, `x[index:index]`, `x(arguments...)`, `x.attribute` | Subscription, slicing, call, attribute reference |
| **3** | `await x` | Await expression |
| **4** | `**` | Exponentiation |
| **5** | `+x`, `-x`, `~x` | Positive, negative, bitwise NOT |
| **6** | `*`, `@`, `/`, `//`, `%` | Multiplication, matrix multiplication, division, floor division, remainder |
| **7** | `+`, `-` | Addition and subtraction |
| **8** | `<<`, `>>` | Bitwise shifts |
| **9** | `&` | Bitwise AND |
| **10** | `^` | Bitwise XOR |
| **11** | `|` | Bitwise OR |
| **12** | `in`, `not in`, `is`, `is not`, `<`, `<=`, `>`, `>=`, `!=`, `==` | Comparisons, including membership tests and identity tests |
| **13** | `not x` | Boolean NOT |
| **14** | `and` | Boolean AND |
| **15** | `or` | Boolean OR |
| **16** | `if – else` | Conditional expression (ternary) |
| **17** | `lambda` | Lambda expression |
| **18 (Lowest)** | `:=` | Assignment expression (Walrus) |
