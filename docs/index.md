---
hide:
  - navigation
  - toc
template: home.html
---

## What's in Here

<div class="grid cards" markdown>

-   :material-book-open-page-variant:{ .lg .middle } __Tutorial__

    ---

    Start here. Step-by-step from zero — installation, basic syntax, data structures, functions, OOP, and advanced patterns.

    [:material-arrow-right: Explore Tutorial](getting-started/whetting-appetite.md)

-   :material-package-variant-closed:{ .lg .middle } __Standard Library__

    ---

    The batteries-included modules: pathlib, datetime, re, json, threading, asyncio, and 30+ more covered in depth.

    [:material-arrow-right: Browse Modules](standard-library/overview.md)

-   :material-code-json:{ .lg .middle } __Language Reference__

    ---

    The formal spec — lexical analysis, data model, execution model, expressions, statements, and the full grammar.

    [:material-arrow-right: View Grammar Spec](language-reference/introduction.md)

-   :material-wrench-outline:{ .lg .middle } __HOWTOs__

    ---

    Practical deep-dives: regex, sorting, logging, Unicode, descriptors, functional programming, and more.

    [:material-arrow-right: Read HOWTOs](howto/regex-howto.md)

-   :material-help-circle-outline:{ .lg .middle } __FAQs__

    ---

    Answers to the most common Python questions — general, programming, design, library, Windows, and GUI.

    [:material-arrow-right: Read Answers](faq/general-faq.md)

-   :material-newspaper-variant-outline:{ .lg .middle } __What's New__

    ---

    Every language change since Python 3.9 — new syntax, removed features, deprecations, and migration notes.

    [:material-arrow-right: Check Releases](whats-new/3.14.md)

-   :material-laptop:{ .lg .middle } __Setup and Usage__

    ---

    Command-line flags, environment variables, Python on Windows / macOS / Linux, virtual environments, and IDEs.

    [:material-arrow-right: Setup Guide](setup-usage/cmdline.md)

-   :material-archive-outline:{ .lg .middle } __Packaging__

    ---

    pip, PyPI, venv, building your own packages, and distributing software to the community.

    [:material-arrow-right: Packaging Guide](packaging/installing-modules.md)

-   :material-cog-outline:{ .lg .middle } __Developer Guide__

    ---

    Extending Python with C, using the Python/C API, and understanding how CPython works under the hood.

    [:material-arrow-right: Developer Docs](extending/extending-with-c.md)

-   :material-alert-circle-outline:{ .lg .middle } __Troubleshooting__

    ---

    Common runtime errors explained — SyntaxError, TypeError, AttributeError, and actionable steps to resolve them.

    [:material-arrow-right: Find Solutions](troubleshooting/common-errors.md)

-   :material-chart-box-outline:{ .lg .middle } __Coverage Status__

    ---

    See all 230+ Python standard library modules at a glance — what's documented, what's partial, and what's planned.

    [:material-arrow-right: View Coverage Matrix](coverage.md)

-   :material-handshake-outline:{ .lg .middle } __Contribute__

    ---

    Help improve ProjectPy — fix typos, contribute examples, write module guides, or design visual diagrams.

    [:material-arrow-right: Contribution Guide](community/contributing.md)

</div>

---

## Quick Example

<div class="example-box" markdown="1">
<div class="example-title">
  <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path d="m8 18-6-6 6-6 1.425 1.425-4.6 4.6L9.4 16.6Zm8 0-1.425-1.425 4.6-4.6L14.6 7.425 16 6l6 6Z"/></svg>
  Example — Your first Python program
</div>

```python
name = input("What's your name? ")
print(f"Hello, {name}! Welcome to Python.")
```

<div class="terminal-output-label">
  <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path d="M20 3H4c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h16c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zm0 16H4V8h16v11zM6 9h2v2H6zm0 4h2v2H6zm4-4h8v2h-8zm0 4h5v2h-5z"/></svg>
  Output
</div>
<div class="terminal-output">What's your name? Alex
Hello, Alex! Welcome to Python.</div>
</div>

!!! tip "Target Python Version"
    This documentation targets **Python 3.14** (the latest stable release). All code examples are verified against it. Version-specific features are marked with badges like <span class="py-version-badge">:material-tag-outline: 3.10+</span>.

---

## Covered Versions

| Version | Status | Documentation Scope |
|:-------:|:-------|:--------------------|
| **3.14** | <span class="py-badge py-badge--success">:material-check-circle: Latest Stable</span> | Complete coverage across tutorials and stdlib |
| **3.13** | <span class="py-badge py-badge--neutral">:material-shield-check: Security Fixes</span> | What's New and compatible stdlib features |
| **3.12** | <span class="py-badge py-badge--neutral">:material-shield-check: Security Fixes</span> | What's New and compatible stdlib features |
| **3.11** | <span class="py-badge py-badge--warning">:material-clock-alert-outline: Maintenance</span> | What's New coverage |
| **3.10** | <span class="py-badge py-badge--danger">:material-alert-circle-outline: End of Life</span> | What's New coverage |
| **3.9**  | <span class="py-badge py-badge--danger">:material-alert-circle-outline: End of Life</span> | What's New coverage |

---

## Open Source

ProjectPy is free open-source software released under the MIT License and maintained by [**Souhardya Das**](https://github.com/Sdas2003810).
If you spot an error, outdated content, or missing topic — [open a pull request](https://github.com/projectpy70/projectpy) or read the [contribution guide](community/contributing.md).
