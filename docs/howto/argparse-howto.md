# Argument Parsing Tutorial HOWTO

This progressive tutorial walks you step-by-step through building command-line utilities using the `argparse` module, from basic arguments to production-ready multi-subcommand CLIs.

---

## Step 1: The Minimal Baseline

Create a minimal parser and parse arguments:

```python
import argparse

parser = argparse.ArgumentParser()
parser.parse_args()
```

Even this minimal script automatically provides `--help` and `-h` flags:

```bash
$ python prog.py --help
usage: prog.py [-h]

options:
  -h, --help  show this help message and exit
```

---

## Step 2: Adding Positional Arguments

Positional arguments are identified by their order on the command line and are **mandatory**:

```python
import argparse

parser = argparse.ArgumentParser(description="Calculate mathematical power.")
parser.add_argument("square", help="Display a square of a given number", type=int)

args = parser.parse_args()
print(args.square ** 2)
```

If the user fails to pass the argument or passes a non-integer, `argparse` displays an error:

```bash
$ python prog.py 4
16

$ python prog.py four
usage: prog.py [-h] square
prog.py: error: argument square: invalid int value: 'four'
```

---

## Step 3: Adding Optional Arguments & Flags

Optional arguments start with `-` or `--`:

```python
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("square", type=int, help="Number to square")
parser.add_argument("-v", "--verbose", action="store_true", help="Increase output verbosity")

args = parser.parse_args()
answer = args.square ** 2

if args.verbose:
    print(f"The square of {args.square} equals {answer}")
else:
    print(answer)
```

Output:

```bash
$ python prog.py 4
16

$ python prog.py 4 --verbose
The square of 4 equals 16
```

---

## Step 4: Multiple Levels of Verbosity

Allow users to specify verbosity with multiple flags (e.g. `-v`, `-vv`, or `-v -v`):

```python
parser.add_argument(
    "-v", "--verbosity",
    action="count",
    default=0,
    help="Increase output verbosity"
)
```

---

## Step 5: Mutually Exclusive Groups

Ensure conflicting options cannot be specified at the same time:

```python
import argparse

parser = argparse.ArgumentParser(description="Calculate power of a number")
parser.add_argument("x", type=int, help="the base")
parser.add_argument("y", type=int, help="the exponent")

# Group options so only one can be chosen
group = parser.add_mutually_exclusive_group()
group.add_argument("-v", "--verbose", action="store_true")
group.add_argument("-q", "--quiet", action="store_true")

args = parser.parse_args()
answer = args.x ** args.y

if args.quiet:
    print(answer)
elif args.verbose:
    print(f"{args.x} to the power {args.y} equals {answer}")
else:
    print(f"{args.x}^{args.y} == {answer}")
```

Running both flags causes an immediate error:

```bash
$ python prog.py 2 3 -v -q
usage: prog.py [-h] [-v | -q] x y
prog.py: error: argument -q/--quiet: not allowed with argument -v/--verbose
```

---

## Step 6: Production CLI with Subcommands

For complex tools with multiple actions (`git commit`, `git clone`), use subparsers:

```python
import argparse

def handle_create(args):
    print(f"Creating project '{args.name}' with template '{args.template}'")

def handle_deploy(args):
    print(f"Deploying to environment: {args.env} (dry-run: {args.dry_run})")

parser = argparse.ArgumentParser(prog="mytool")
subparsers = parser.add_subparsers(dest="command", required=True)

# Subparser 1: create
p_create = subparsers.add_parser("create", help="Create a new project")
p_create.add_argument("name", help="Project name")
p_create.add_argument("--template", default="standard", choices=["standard", "minimal"])
p_create.set_defaults(func=handle_create)

# Subparser 2: deploy
p_deploy = subparsers.add_parser("deploy", help="Deploy project to cloud")
p_deploy.add_argument("--env", choices=["staging", "production"], default="staging")
p_deploy.add_argument("--dry-run", action="store_true")
p_deploy.set_defaults(func=handle_deploy)

args = parser.parse_args()
# Dispatch directly to the registered handler function:
args.func(args)
```
