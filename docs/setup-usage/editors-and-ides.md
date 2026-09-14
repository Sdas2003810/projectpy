# Editors, IDEs & Developer Tooling

Choosing the right development environment dramatically improves productivity with intelligent autocompletion, real-time linting, inline type checking, and interactive debugging.

---

## Major Python IDEs & Editors

### 1. Visual Studio Code

VS Code is the most popular editor for Python development.

- **Recommended Extensions**:
  - `ms-python.python`: Official Python language support.
  - `ms-python.vscode-pylance`: High-performance language server (types, docstrings, refactorings).
  - `charliermarsh.ruff`: Lightning-fast linting and formatting.
- **Selecting Interpreter**: Press `Ctrl+Shift+P` (or `Cmd+Shift+P` on Mac), type **Python: Select Interpreter**, and pick your `.venv` virtual environment.

Example `.vscode/settings.json` configuration:

```json
{
  "[python]": {
    "editor.defaultFormatter": "charliermarsh.ruff",
    "editor.formatOnSave": true,
    "editor.codeActionsOnSave": {
      "source.fixAll": "explicit",
      "source.organizeImports": "explicit"
    }
  },
  "python.analysis.typeCheckingMode": "basic"
}
```

---

### 2. PyCharm (JetBrains)

PyCharm is a dedicated, full-featured Python IDE available in free Community and paid Professional editions.

- Out-of-the-box virtual environment and poetry/conda management.
- Visual graphical debugger with variable inspection and breakpoint evaluation.
- Built-in database tools, memory profiler, and refactoring suite.

---

### 3. Neovim & Modern Terminal Editors

Developers using Neovim or Helix rely on the **Language Server Protocol (LSP)**:

- **Pyright / Basedpyright**: Type checking and semantic token highlighting.
- **Ruff LSP**: Instant lint diagnostics and formatting on save.

---

### 4. IDLE (Built-in)

Python includes **IDLE** (Integrated Development and Learning Environment) with every standard installation. It is written in Python using `tkinter`.

To launch IDLE:

```bash
python -m idlelib
```

It includes a syntax-highlighting editor, multi-window text editing, a visual debugger, and an interactive shell — ideal for beginners without setup friction.

---

## Modern Tooling Suite

Modern Python projects standardize on a fast, unified toolchain:

| Tool | Role | Why It's Recommended |
| :--- | :--- | :--- |
| **Ruff** | Linter & Formatter | Written in Rust; runs 10-100x faster than Black, Flake8, and isort combined |
| **mypy** / **pyright** | Static Type Checker | Detects type mismatches and None-dereferences before deployment |
| **pytest** | Test Framework | Concise assertions (`assert a == b`), rich fixtures, and extensive plugin ecosystem |
| **uv** | Package & Environment Manager | Instant environment creation and pip installations |

---

## Centralized Configuration with `pyproject.toml`

Modern Python projects consolidate configuration for all tools into a single `pyproject.toml` file at the repository root:

```toml
[project]
name = "my-awesome-app"
version = "0.1.0"
requires-python = ">=3.11"
dependencies = [
    "requests>=2.31.0",
]

[tool.ruff]
line-length = 88
target-version = "py312"

[tool.ruff.lint]
select = ["E", "F", "I", "UP", "B"]

[tool.pytest.ini_options]
testpaths = ["tests"]
addopts = "-v --strict-markers"

[tool.mypy]
strict = true
warn_unused_configs = true
```
