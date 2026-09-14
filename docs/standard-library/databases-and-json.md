# Data Formats and Databases (JSON, CSV, SQLite)

Python includes built-in modules for parsing structured data formats and managing embedded relational databases without external dependencies.

---

## 1. The `json` Module

JSON (JavaScript Object Notation) is the most widely used data-interchange format on the web.

### Key Functions

| Function | Operation |
| :--- | :--- |
| `json.dumps(obj)` | Converts Python object to JSON formatted string |
| `json.loads(str)` | Parses JSON formatted string into a Python object |
| `json.dump(obj, f)` | Serializes Python object directly into an open file pointer |
| `json.load(f)` | Reads and parses JSON directly from an open file pointer |

<div class="example-box">
<div class="example-title">Example: JSON Serialization and Deserialization</div>

```python
import json

data = {
    "title": "ProjectPy",
    "version": 1.0,
    "tags": ["python", "documentation"],
    "published": True
}

# Serialize to string with indentation
json_str = json.dumps(data, indent=2)
print("JSON Output:\n", json_str)

# Parse JSON string back to dictionary
parsed = json.loads(json_str)
print("Parsed Title:", parsed["title"])
```
</div>

---

## 2. The `csv` Module

The `csv` module handles reading and writing tabular spreadsheet data in Comma-Separated Values format.

### Using `DictReader` and `DictWriter` (Recommended)

<div class="example-box">
<div class="example-title">Example: Writing and Reading CSV Files</div>

```python
import csv
from pathlib import Path

csv_path = Path("students.csv")

# 1. Writing with DictWriter
fieldnames = ["id", "name", "score"]
rows = [
    {"id": 1, "name": "Alice", "score": 95},
    {"id": 2, "name": "Bob", "score": 88},
    {"id": 3, "name": "Charlie", "score": 92}
]

with open(csv_path, "w", newline="", encoding="utf-8") as f:
    writer = csv.DictWriter(f, fieldnames=fieldnames)
    writer.writeheader()
    writer.writerows(rows)

# 2. Reading with DictReader
with open(csv_path, "r", encoding="utf-8") as f:
    reader = csv.DictReader(f)
    for row in reader:
        print(f"Student: {row['name']} scored {row['score']}")
```
</div>

---

## 3. The `sqlite3` Module

Python ships with a complete, serverless, self-contained relational SQL database engine called **SQLite**.

### Key Workflow Steps
1. Connect to database file (or `":memory:"` for an in-memory database).
2. Create a cursor.
3. Execute SQL statements.
4. Commit changes.
5. Close connection.

<div class="example-box">
<div class="example-title">Example: Complete SQLite CRUD Operations</div>

```python
import sqlite3

# Connect to in-memory database
conn = sqlite3.connect(":memory:")
cursor = conn.cursor()

# 1. Create a table
cursor.execute("""
CREATE TABLE users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    username TEXT NOT NULL,
    email TEXT UNIQUE NOT NULL
);
""")

# 2. Insert records using parameterized queries (prevents SQL Injection!)
users_to_add = [
    ("alice_dev", "alice@example.com"),
    ("bob_coder", "bob@example.com")
]
cursor.executemany("INSERT INTO users (username, email) VALUES (?, ?);", users_to_add)
conn.commit()

# 3. Query records
cursor.execute("SELECT id, username, email FROM users WHERE id = ?", (1,))
user = cursor.fetchone()
print("Found User:", user)

# Close connection
conn.close()
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
Found User: (1, 'alice_dev', 'alice@example.com')
</div>
</div>
