# General Python FAQ

Frequently asked general questions about Python, its governance, philosophy, licensing, and community.

---

## What is Python?

Python is an interpreted, interactive, object-oriented, high-level programming language. It incorporates modules, exceptions, dynamic typing, very high-level dynamic data types, and classes. It supports multiple programming paradigms, including structured (procedural), object-oriented, and functional programming.

Python combines remarkable power with very clear syntax. It has interfaces to many system calls and libraries, as well as to various window systems, and is extensible in C, C++, or Rust.

---

## What is the Python Software Foundation (PSF)?

The **Python Software Foundation (PSF)** is an independent 501(c)(3) non-profit corporation that holds the intellectual property rights behind Python. The PSF manages open-source licensing, funds PyPI infrastructure, organizes PyCon US, and awards grants to support community conferences and education worldwide.

---

## Are there copyright restrictions on the use of Python?

No. Python is distributed under the **Python Software Foundation License (PSFL)**, an open-source, OSI-approved, BSD-style permissive license.

- You may freely download, modify, and distribute Python in source or binary forms.
- You **can** embed Python into commercial, proprietary, closed-source software products without paying royalties or fees.
- You must retain the PSF copyright notice in the source distributions.

---

## Why is it called Python?

When Guido van Rossum began implementing Python in December 1989, he was also reading the published scripts from **"Monty Python's Flying Circus"**, a BBC comedy series from the 1970s. Van Rossum thought he needed a name that was short, unique, and slightly mysterious, so he decided to call the language Python.

Traditional tutorial examples frequently pay homage to Monty Python sketches (using names like `spam`, `eggs`, and `knights` instead of `foo` and `bar`).

---

## How stable is Python?

Extremely stable. New major feature versions (e.g., 3.12, 3.13, 3.14) are released on an annual cycle every October following an extensive schedule of alpha, beta, and release-candidate test cycles.

- Each minor version receives bugfix updates for approximately 18–24 months.
- After active bugfixes end, each version receives security-only fixes for a total lifecycle of **5 years**.
- Breaking syntax changes require the formal **Python Enhancement Proposal (PEP)** process and two minor releases of deprecation warnings before removal.

---

## How does Python's version numbering scheme work?

Python releases are numbered as `A.B.C`:
- `A` is the **major version**: Incremented only for fundamentally incompatible paradigm changes (e.g., Python 2 to Python 3).
- `B` is the **minor version**: Released annually; introduces new syntax, language features, and standard library modules.
- `C` is the **micro version**: Bugfix and security patches that strictly maintain backward compatibility.

---

## How does Python compare to other languages?

| Language | Dynamic / Static | Memory Management | Primary Strengths |
| :--- | :--- | :--- | :--- |
| **Python** | Dynamic | Automatic GC (Ref count + cycle detector) | Rapid development, read-write speed, AI/ML, automation |
| **C / C++** | Static | Manual | Bare-metal execution speed, low memory footprint |
| **Java / C#**| Static | Automatic GC | Large enterprise monoliths, strict static types |
| **Rust** | Static | Compile-time borrow checker | Memory safety without a garbage collector |
| **Go** | Static | Automatic GC | Microservices, concurrency, fast compile times |
