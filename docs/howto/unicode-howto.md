# Unicode HOWTO

One of the greatest engineering achievements of Python 3 was creating a strict, unambiguous boundary between text (**`str`**) and raw binary data (**`bytes`**).

This guide demystifies Unicode, encodings, code points, and best practices for preventing `UnicodeDecodeError`.

---

## The Fundamentals: Characters vs Code Points vs Bytes

Understanding text processing requires separating three concepts:

1. **Character**: An abstract symbol (e.g. the letter `A`, the Greek letter `Ω`, or the emoji `🐍`).
2. **Code Point**: An integer uniquely assigned to each character by the Unicode Consortium. Written in hexadecimal as `U+XXXX` (e.g. `A` is `U+0041`, `🐍` is `U+1F40D`).
3. **Bytes**: The physical 8-bit bytes stored in RAM or on disk. A single Unicode character can require between 1 and 4 bytes when encoded in UTF-8.

```
+-----------------------------------------------------------+
|  Abstract Character:  🐍                                 |
|  Unicode Code Point:  U+1F40D (decimal: 128013)           |
|  UTF-8 Encoded Bytes: b'\xf0\x9f\x90\x8d' (4 raw bytes)   |
+-----------------------------------------------------------+
```

---

## Python's Types: `str` vs `bytes`

- **`str`**: Represents human-readable text. It stores a sequence of Unicode characters.
- **`bytes`**: Represents raw machine data. It stores a sequence of integers from 0 to 255.

You cannot concatenate strings and bytes without explicit conversion:

```python
# Decoding: bytes -> str
byte_data = b"\xe2\x9c\x93 OK"  # UTF-8 encoded bytes
text = byte_data.decode("utf-8")
print(text)  # "✓ OK"

# Encoding: str -> bytes
greeting = "Hello, 世界"
encoded_bytes = greeting.encode("utf-8")
print(encoded_bytes)  # b'Hello, \xe4\xb8\x96\xe7\x95\x8c'
```

---

## The "Unicode Sandwich" Architecture

The fundamental design rule for writing robust software is the **Unicode Sandwich**:

```
[ Disk / Network / APIs ]  (Raw bytes)
           |
           v  DECODE to Unicode `str` as early as possible
+------------------------------------+
|  Application Core Logic (Meat)     |  Process 100% of data
|  Clean, native Python `str`        |  as Unicode strings!
+------------------------------------+
           |
           v  ENCODE back to `bytes` at the very last moment
[ Disk / Network / APIs ]  (Raw bytes)
```

---

## Handling Encoding Errors

When decoding legacy data or corrupted inputs, specify the `errors` parameter:

```python
corrupted_bytes = b"Price: \xa3100"  # \xa3 is British Pound in ISO-8859-1, invalid in UTF-8

# 1. 'strict' (Default): Raises UnicodeDecodeError immediately
# corrupted_bytes.decode("utf-8")

# 2. 'replace': Inserts the official Unicode replacement character ()
print(corrupted_bytes.decode("utf-8", errors="replace"))
# "Price: 100"

# 3. 'ignore': Drops bad bytes silently
print(corrupted_bytes.decode("utf-8", errors="ignore"))
# "Price: 100"

# 4. 'backslashreplace': Escapes invalid bytes as hex sequences
print(corrupted_bytes.decode("utf-8", errors="backslashreplace"))
# "Price: \xa3100"
```

---

## Unicode Normalization with `unicodedata`

In Unicode, the same visible glyph can be represented in multiple ways. For example, the accented letter `é` can be:
- A single pre-composed code point: `U+00E9` (`\u00e9`).
- Two combining code points: the letter `e` (`U+0065`) followed by the combining acute accent (`U+0301`).

Visually they look identical, but standard Python equality fails!

```python
import unicodedata

s1 = "\u00e9"         # "é" (pre-composed)
s2 = "e\u0301"        # "e" + acute accent

print(s1 == s2)       # False! Bug alert!

# Normalize both strings using NFC (Canonical Decomposition followed by Canonical Composition)
clean_s1 = unicodedata.normalize("NFC", s1)
clean_s2 = unicodedata.normalize("NFC", s2)

print(clean_s1 == clean_s2)  # True!
```

### Inspecting Characters with `unicodedata.name()`

```python
import unicodedata

char = "🐍"
print(unicodedata.name(char))  # "SNAKE"

# Lookup character by Unicode name
lookup_char = unicodedata.lookup("ROCKET")
print(lookup_char)  # "🚀"
```
