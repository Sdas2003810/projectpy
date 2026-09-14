# Full Grammar Specification & The PEG Parser

Python's syntax is formally defined by a formal grammar specification. Since Python 3.9 (PEP 617), CPython has used a **Parsing Expression Grammar (PEG)** parser, replacing the original LL(1)-based parser that governed Python syntax for nearly thirty years.

---

## The Transition to the PEG Parser (PEP 617)

The original LL(1) parser suffered from severe architectural limitations:
- It could only look ahead one token (`k=1`) when deciding which grammar rule to parse.
- Left recursion was prohibited, forcing language designers to use unnatural hacks or delay modern syntax features (such as parenthesized context managers).

The modern PEG parser introduced in Python 3.9 provides:
- **Arbitrary Lookahead**: The parser can look ahead multiple tokens or evaluate semantic actions before committing to a branch.
- **Ordered Choices**: In PEG, choices (`A | B`) are evaluated sequentially. The first matching rule succeeds, eliminating grammar ambiguities.
- **Direct Support for Left Recursion**: Through memoization (Packrat parsing), PEG handles left-recursive grammar rules cleanly and efficiently.

---

## Grammar Notation

The CPython grammar file (`Grammar/python.gram`) is written in a PEG notation with semantic actions:

| Symbol | Meaning | Example |
| :--- | :--- | :--- |
| `rule: e` | Rule definition | `file: statements? $` |
| `e1 e2` | Sequence: match `e1` then `e2` | `NAME '=' expr` |
| `e1 \| e2` | Ordered choice: try `e1`; if it fails, try `e2` | `'if' \| 'while'` |
| `e?` | Optional: match zero or one time | `('as' NAME)?` |
| `e*` | Zero or more repetitions | `statement*` |
| `e+` | One or more repetitions | `digit+` |
| `&e` | Positive lookahead: match `e` without consuming tokens | `&'async'` |
| `!e` | Negative lookahead: succeed only if `e` does not match | `!'else'` |
| `s.e+` | Repetition separated by separator `s` | `','.NAME+` |

---

## Core Grammar Definitions (Abridged)

Below is an abridged representation of the core top-level grammar rules governing Python:

```peg
# Top-level entry points
file: [statements] $
interactive: [statement_newline]
eval: expressions NEWLINE* $

# Statements
statements: statement+
statement: compound_stmt | simple_stmts

simple_stmts:
    | simple_stmt !';' NEWLINE
    | ';'.simple_stmt+ [';'] NEWLINE

simple_stmt:
    | assignment
    | type_alias
    | star_expressions
    | return_stmt
    | import_stmt
    | raise_stmt
    | 'pass'
    | del_stmt
    | yield_stmt
    | assert_stmt
    | 'break'
    | 'continue'
    | global_stmt
    | nonlocal_stmt

compound_stmt:
    | function_def
    | if_stmt
    | class_def
    | with_stmt
    | for_stmt
    | try_stmt
    | while_stmt
    | match_stmt

# Control flow compound statements
if_stmt:
    | 'if' named_expression ':' block elif_stmt
    | 'if' named_expression ':' block [else_block]

while_stmt:
    | 'while' named_expression ':' block [else_block]

for_stmt:
    | 'for' star_targets 'in' ~ star_expressions ':' [TYPE_COMMENT] block [else_block]
    | ASYNC 'for' star_targets 'in' ~ star_expressions ':' [TYPE_COMMENT] block [else_block]

with_stmt:
    | 'with' '(' ','.with_item+ [','] ')' ':' block
    | 'with' ','.with_item+ ':' [TYPE_COMMENT] block
    | ASYNC 'with' '(' ','.with_item+ [','] ')' ':' block
    | ASYNC 'with' ','.with_item+ ':' [TYPE_COMMENT] block

match_stmt:
    | "match" subject_expr ':' NEWLINE INDENT case_block+ DEDENT

# Expressions & Operators
expressions:
    | expression (',' expression )+ [',']
    | expression ','
    | expression

expression:
    | disjunction 'if' disjunction 'else' expression
    | disjunction
    | lambdef

disjunction:
    | conjunction ('or' conjunction )+
    | conjunction

conjunction:
    | inversion ('and' inversion )+
    | inversion

inversion:
    | 'not' inversion
    | comparison

comparison:
    | bitwise_or compare_op_bitwise_or_pair+
    | bitwise_or

sum:
    | sum '+' term
    | sum '-' term
    | term

term:
    | term '*' factor
    | term '/' factor
    | term '//' factor
    | term '%' factor
    | term '@' factor
    | factor

factor:
    | '+' factor
    | '-' factor
    | '~' factor
    | power

power:
    | await_primary '**' factor
    | await_primary

primary:
    | primary '.' NAME
    | primary '(' [arguments] ')'
    | primary '[' slices ']'
    | atom

atom:
    | NAME
    | 'True'
    | 'False'
    | 'None'
    | strings
    | NUMBER
    | (tuple | group | genexp)
    | (list | listcomp)
    | (dict | set | dictcomp | setcomp)
    | '...'
```

---

## Inspecting the Syntax Tree with the `ast` Module

Python exposes its internal parser and Abstract Syntax Tree (AST) directly to user code via the built-in `ast` module:

```python
import ast

code_snippet = "result = (a + b) * 2"

# Parse code string into AST
tree = ast.parse(code_snippet)

# Inspect formatted AST structure
print(ast.dump(tree, indent=2))
```

<div class="terminal-output">
Module(
  body=[
    Assign(
      targets=[
        Name(id='result', ctx=Store())],
      value=BinOp(
        left=BinOp(
          left=Name(id='a', ctx=Load()),
          op=Add(),
          right=Name(id='b', ctx=Load())),
        op=Mult(),
        right=Constant(value=2)))])
</div>

Using `ast.parse()`, tools like linters, static analyzers, and code formatters (like Black and Ruff) can safely inspect and manipulate Python code according to its formal grammar rules.
