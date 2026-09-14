# How CPython Works Under the Hood

CPython is the reference implementation of the Python programming language, written in C. When you type `python app.py`, your human-readable source code undergoes a multi-stage compilation and execution pipeline before executing on your physical CPU.

---

## The 5 Execution Phases of CPython

```
[ Source Code (.py) ]
          |
          v 1. Lexical Analysis (Tokenizer)
    [ Token Stream ]
          |
          v 2. Syntactic Analysis (PEG Parser)
    [ Abstract Syntax Tree (AST) ]
          |
          v 3. Compilation (Symbol Table & Bytecode Generator)
    [ Bytecode Instructions (.pyc) ]
          |
          v 4. Virtual Machine Evaluation Loop (ceval.c)
    [ Frame Stack & Execution Engine ]
          |
          v 5. Hardware / Native CPU Execution
```

---

## Phase 1 & 2: Tokenizing and Parsing (The PEG Parser)

1. **Tokenizer (`Parser/tokenizer.c`)**: Reads raw source characters and breaks them into discrete tokens (`NAME`, `NUMBER`, `STRING`, `NEWLINE`, `INDENT`, `DEDENT`).
2. **PEG Parser (`Parser/parser.c`)**: Validates token sequences against Python's grammar rules and builds an **Abstract Syntax Tree (AST)**.

Inspect tokens using the built-in `tokenize` module:

```bash
python -m tokenize -e script.py
```

Inspect the AST using the built-in `ast` module:

```python
import ast
tree = ast.parse("result = 10 + 20")
print(ast.dump(tree, indent=2))
```

---

## Phase 3: Bytecode Generation & `__pycache__`

CPython compiles the AST into **bytecode** — compact, platform-independent numeric instructions designed for an emulated virtual CPU.

To prevent re-compiling unmodified code on every run, Python caches compiled bytecode files as `.pyc` files inside a local `__pycache__/` folder:
- **Magic Number**: The first 4 bytes indicate the exact Python version.
- **Header**: Stores modification timestamp or git hash.
- **Code Object**: Serialized bytecode marshaled into binary format.

---

## Phase 4: Disassembling Bytecode with `dis`

The Python Virtual Machine is a **stack-based machine**: operations push operands onto an evaluation stack and pop results.

Use the `dis` module to disassemble any Python function into its underlying bytecode:

```python
import dis

def calculate(a, b):
    return a + b * 2

dis.dis(calculate)
```

Output:

<div class="terminal-output">
  2           0 RESUME                   0

  3           2 LOAD_FAST                0 (a)
              4 LOAD_FAST                1 (b)
              6 LOAD_CONST               1 (2)
              8 BINARY_OP                5 (*)
             12 BINARY_OP                0 (+)
             16 RETURN_VALUE
</div>

### How the Evaluation Stack Executes `a + b * 2`:

1. `LOAD_FAST 0 (a)`: Pushes value of `a` onto stack: `[a]`
2. `LOAD_FAST 1 (b)`: Pushes value of `b` onto stack: `[a, b]`
3. `LOAD_CONST 1 (2)`: Pushes constant `2` onto stack: `[a, b, 2]`
4. `BINARY_OP (*)`: Pops `2` and `b`, multiplies them, pushes result: `[a, (b*2)]`
5. `BINARY_OP (+)`: Pops `(b*2)` and `a`, adds them, pushes sum: `[(a + b*2)]`
6. `RETURN_VALUE`: Pops final sum and returns to caller.

---

## Modern CPython Architecture: The Faster CPython Project

Since Python 3.11, the core development team (led by Mark Shannon and Guido van Rossum) has revolutionized CPython's execution engine:

### 1. Specializing Adaptive Interpreter (Python 3.11+, PEP 659)

In standard Python, `BINARY_OP (+)` has to handle integers, floats, strings, lists, or custom overloaded objects. On every addition, it checks object types.

The Adaptive Interpreter monitors bytecode execution:
- If an addition instruction is repeatedly called with two integers, CPython dynamically rewrites the opcode in-place into **`BINARY_OP_ADD_INT`**.
- This specialized opcode skips all generic type checks and executes raw integer addition in C directly, achieving significant performance speedups.

### 2. Copy-and-Patch JIT Compiler (Python 3.13+)

Python 3.13 introduced an experimental **Just-In-Time (JIT)** compiler:
- Groups frequently executed specialized bytecode sequences into "traces" (Tier 2 micro-ops).
- Emits native machine code directly to the host CPU, bypassing bytecode interpretation entirely.

### 3. Free-Threaded CPython (Python 3.13+, PEP 703)

Removes the Global Interpreter Lock (GIL):
- Replaces global mutex with thread-safe **mimalloc** memory allocator.
- Uses biased reference counting to eliminate thread synchronization overhead on thread-local objects.
- Enables true multi-core parallel thread execution.
