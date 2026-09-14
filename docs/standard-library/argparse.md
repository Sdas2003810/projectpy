# `argparse` — Command-Line Parsing

The `argparse` module is Python's standard tool for building robust command-line interfaces (CLIs). It handles argument parsing, generates formatted help screens (`--help`), validates types and permitted choices, and emits descriptive error messages when users pass invalid arguments.

---

## The Core Workflow

Building a CLI with `argparse` involves three steps:

1. Create an `ArgumentParser` object.
2. Register arguments with `.add_argument()`.
3. Parse the arguments with `.parse_args()`.

```python
import argparse

# 1. Create parser
parser = argparse.ArgumentParser(
    prog="databackup",
    description="Backup database tables to an encrypted S3 bucket."
)

# 2. Add arguments
# Positional argument (required)
parser.add_argument("table", help="Name of the database table to backup")

# Optional argument with a default value
parser.add_argument(
    "-c", "--compression",
    choices=["gzip", "zstd", "none"],
    default="gzip",
    help="Compression algorithm (default: gzip)"
)

# Boolean flag
parser.add_argument(
    "--dry-run",
    action="store_true",
    help="Simulate the backup without writing data"
)

# 3. Parse arguments
# In a real script, parse_args() automatically inspects sys.argv[1:]
args = parser.parse_args(["users", "--compression", "zstd", "--dry-run"])

print(f"Table: {args.table}")
print(f"Compression: {args.compression}")
print(f"Dry run mode: {args.dry_run}")
```

<div class="terminal-output">
Table: users
Compression: zstd
Dry run mode: True
</div>

---

## Positional vs Optional Arguments

- **Positional Arguments**: Identified by position on the command line without leading dashes. They are required by default:
  ```python
  parser.add_argument("filename", help="File to process")
  ```
- **Optional Arguments / Flags**: Prefixed with `-` (short flag) or `--` (long option):
  ```python
  parser.add_argument("-o", "--output", help="Destination file path")
  ```

---

## Type Conversion and Validation

Pass `type=` to convert strings from the command line into native Python types automatically:

```python
from pathlib import Path

parser = argparse.ArgumentParser()

# Convert to integer
parser.add_argument("--port", type=int, default=8000, help="Listening port")

# Convert directly to a pathlib.Path object
parser.add_argument("--config", type=Path, default=Path("config.toml"))

# Custom type validator function
def positive_int(val: str) -> int:
    num = int(val)
    if num <= 0:
        raise argparse.ArgumentTypeError(f"{val} must be a positive integer")
    return num

parser.add_argument("--retries", type=positive_int, default=3)
```

If a user passes `--port abc`, `argparse` automatically catches the conversion failure and prints:
<div class="terminal-output">
usage: prog.py [-h] [--port PORT]
prog.py: error: argument --port: invalid int value: 'abc'
</div>

---

## Handling Boolean Flags

### Store True / False

```python
parser.add_argument("--verbose", action="store_true", help="Enable verbose logs")
parser.add_argument("--no-cache", action="store_false", dest="cache", help="Disable caching")
```

### Modern `BooleanOptionalAction` (Python 3.9+)

Automatically generates both positive (`--ssl`) and negative (`--no-ssl`) switches:

```python
parser.add_argument(
    "--ssl",
    action=argparse.BooleanOptionalAction,
    default=True,
    help="Enable or disable SSL encryption"
)
```

Users can pass either `--ssl` or `--no-ssl`.

---

## Counting Occurrences: `action="count"`

Commonly used for verbosity levels (e.g. `-v`, `-vv`, `-vvv`):

```python
parser.add_argument(
    "-v", "--verbose",
    action="count",
    default=0,
    help="Increase output verbosity (-v, -vv, -vvv)"
)

args = parser.parse_args(["-vvv"])
print(f"Verbosity level: {args.verbose}")  # 3
```

---

## Mutually Exclusive Groups

Ensure that two conflicting flags cannot be passed simultaneously:

```python
group = parser.add_mutually_exclusive_group()
group.add_argument("--quiet", action="store_true", help="Print minimal output")
group.add_argument("--debug", action="store_true", help="Print debug diagnostics")

# If user runs: python script.py --quiet --debug
# error: argument --debug: not allowed with argument --quiet
```

---

## Subcommands with `add_subparsers()`

Modern tools (like `git`, `docker`, and `kubectl`) organize operations into subcommands (`git commit`, `git push`, `docker run`):

```python
import argparse

parser = argparse.ArgumentParser(prog="cloudcli")
subparsers = parser.add_subparsers(dest="command", required=True)

# Subcommand: start
start_parser = subparsers.add_parser("start", help="Start an instance")
start_parser.add_argument("instance_id", help="Target instance ID")
start_parser.add_argument("--force", action="store_true")

# Subcommand: stop
stop_parser = subparsers.add_parser("stop", help="Stop a running instance")
stop_parser.add_argument("instance_id", help="Target instance ID")

args = parser.parse_args(["start", "i-098234af", "--force"])
print(f"Action: {args.command} -> Instance: {args.instance_id}, Force: {args.force}")
```

<div class="terminal-output">
Action: start -> Instance: i-098234af, Force: True
</div>

---

## Auto-Generated Help Display

When the user runs `python script.py --help` or `-h`, `argparse` automatically formats and outputs:

<div class="terminal-output">
usage: cloudcli [-h] {start,stop} ...

positional arguments:
  {start,stop}
    start        Start an instance
    stop         Stop a running instance

options:
  -h, --help     show this help message and exit
</div>
