# I/O Streams and Serialization (`io`, `json`, `pickle`, `csv`)

Data handling in Python relies on two complementary concepts: **I/O Streams** (which model streams of bytes and characters) and **Serialization** (which converts in-memory Python objects into byte/string formats for storage or network transport).

---

## The `io` Module: In-Memory Streams

The `io` module provides core Python stream implementations. Instead of creating physical temporary files on disk to pass to APIs that expect file-like objects, you can use in-memory stream buffers:

### `io.StringIO` — Text Streams in Memory

An in-memory file-like object for unicode text:

```python
import io

# Create an in-memory text stream
buffer = io.StringIO()
buffer.write("First line of logs.\n")
buffer.write("Second line of logs.\n")

# Retrieve entire buffer content as a string
content = buffer.getvalue()
print(content)

# Reset cursor to beginning to read sequentially
buffer.seek(0)
for line in buffer:
    print(f"Read: {line.strip()}")

buffer.close()
```

### `io.BytesIO` — Binary Streams in Memory

An in-memory stream for raw binary data (ideal for image generation, zip archives, or network payloads):

```python
import io
import zipfile

# Create a zip archive completely in RAM
zip_buffer = io.BytesIO()

with zipfile.ZipFile(zip_buffer, "w", zipfile.ZIP_DEFLATED) as archive:
    archive.writestr("notes.txt", "Important meeting notes")
    archive.writestr("config.ini", "[Settings]\nTheme=Dark")

# Get raw zip bytes ready to send over HTTP or save to disk
raw_zip_data = zip_buffer.getvalue()
print(f"Created in-memory zip archive: {len(raw_zip_data)} bytes")
```

---

## Data Serialization Formats

### 1. `json` — Universal Interoperability

JSON (JavaScript Object Notation) is the de facto standard for web APIs, configuration files, and cross-language communication.

```python
import json

payload = {
    "user_id": 1042,
    "username": "souhardya",
    "is_active": True,
    "tags": ["developer", "python"],
    "preferences": None
}

# Serialize to string (dumps) and file (dump)
json_string = json.dumps(payload, indent=2)
print(json_string)

# Deserialize from string (loads)
parsed = json.loads(json_string)
print(parsed["username"])  # souhardya
```

#### Custom Serialization with `default=`

Standard JSON only supports `dict`, `list`, `str`, `int`, `float`, `bool`, and `None`. To serialize custom objects, pass a handler function:

```python
from datetime import datetime, date

class Event:
    def __init__(self, title: str, timestamp: datetime):
        self.title = title
        self.timestamp = timestamp

def custom_encoder(obj):
    if isinstance(obj, (datetime, date)):
        return obj.isoformat()
    if hasattr(obj, "__dict__"):
        return obj.__dict__
    raise TypeError(f"Object of type {type(obj)} is not JSON serializable")

ev = Event("Release Party", datetime(2026, 9, 14, 18, 0, 0))
json_output = json.dumps(ev, default=custom_encoder, indent=2)
print(json_output)
```

---

### 2. `pickle` — Native Python Object Serialization

The `pickle` module serializes arbitrary Python objects (including class definitions, functions, and nested structures) into a Python-specific binary format:

```python
import pickle

data_structure = {
    "numbers": {1, 2, 3, 4},  # Sets are supported by pickle (unlike JSON)
    "complex": 3 + 4j,
    "lambda_test": ("tuple", [10, 20])
}

# Serialize to bytes
serialized_bytes = pickle.dumps(data_structure)

# Restore object
restored = pickle.loads(serialized_bytes)
print(restored["numbers"])  # {1, 2, 3, 4}
```

!!! danger "Security Warning: Never Unpickle Untrusted Data!"
    The `pickle` format is **not secure**. When deserializing with `pickle.loads()`, arbitrary CPython bytecodes and constructor calls can execute on your system. A malicious payload can execute shell commands (`os.system("rm -rf /")`). Use JSON or Protocol Buffers when handling untrusted user input.

---

### 3. `csv` — Tabular Data Exchange

The `csv` module simplifies reading and writing comma-delimited data files without manual string splitting:

=== "Using `csv.DictReader` & `csv.DictWriter`"
    ```python
    import csv
    import io

    # Writing tabular data
    output = io.StringIO()
    fields = ["id", "product", "price"]
    writer = csv.DictWriter(output, fieldnames=fields)
    writer.writeheader()
    writer.writerow({"id": 1, "product": "Mechanical Keyboard", "price": 129.99})
    writer.writerow({"id": 2, "product": "USB-C Hub", "price": 45.00})

    # Reading back tabular data
    output.seek(0)
    reader = csv.DictReader(output)
    for row in reader:
        print(f"Item: {row['product']} - ${float(row['price']):.2f}")
    ```

=== "Using Basic `csv.reader` & `csv.writer`"
    ```python
    import csv

    data = [
        ["Name", "Role", "Department"],
        ["Alice", "Architect", "Engineering"],
        ["Bob", "Lead", "DevOps"]
    ]

    with open("team.csv", "w", newline="", encoding="utf-8") as f:
        writer = csv.writer(f)
        writer.writerows(data)
    ```

!!! note "The `newline=''` Argument"
    When opening files for the `csv` module on Windows, always specify `newline=""` in `open()`. Without it, Python's universal newline translation may insert extra empty rows (`\r\r\n`).

---

## Serialization Format Comparison

| Format | Module | Cross-Language? | Human Readable? | Arbitrary Objects? | Safe on Untrusted Input? |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **JSON** | `json` | Yes | Yes | No (requires custom encoders) | Yes |
| **Pickle** | `pickle` | No (Python only) | No (Binary) | Yes (almost all Python types) | **No** (Remote Code Execution) |
| **CSV** | `csv` | Yes | Yes | Tabular only | Yes |
| **Shelve** | `shelve` | No | No (DBM/Binary) | Yes (uses pickle) | **No** |
| **Toml** | `tomllib` (3.11+) | Yes | Yes | Read-only config | Yes |
