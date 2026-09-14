# Distributing & Publishing Packages

Sharing your Python libraries with the global developer community through the **Python Package Index (PyPI)** has never been cleaner. Modern packaging follows declarative standards defined in **PEP 517**, **PEP 518**, and **PEP 621**, using a single unified `pyproject.toml` file.

---

## Recommended Project Structure: The `src` Layout

The Python Packaging Authority (PyPA) strongly recommends the **`src` layout**:

```
my_package/
├── pyproject.toml       # Central packaging configuration
├── README.md            # Markdown documentation shown on PyPI
├── LICENSE              # License file (e.g. MIT, Apache 2.0)
├── src/
│   └── my_package/      # Your actual package importable by Python
│       ├── __init__.py
│       └── core.py
└── tests/
    └── test_core.py
```

!!! tip "Why the `src` Layout is Superior"
    If your package files are in the repository root (`./my_package/`), running `pytest` will import the local uninstalled code from the current working directory, masking packaging bugs. With the `src` layout, tests must import the installed package from the virtual environment, guaranteeing your package builds and installs correctly.

---

## The Modern `pyproject.toml` Specification

Create `pyproject.toml` at the root of your repository:

```toml
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[project]
name = "hypercalc"
version = "1.0.0"
description = "A high-performance numerical calculator library"
readme = "README.md"
license = { text = "MIT" }
authors = [
    { name = "Alice Dev", email = "alice@example.com" }
]
requires-python = ">=3.10"
dependencies = [
    "requests>=2.30.0",
]

[project.optional-dependencies]
dev = [
    "pytest>=8.0",
    "ruff>=0.4.0",
    "mypy>=1.10.0"
]

# Create command-line CLI shortcuts automatically upon installation
[project.scripts]
hypercalc = "my_package.core:main_cli"

[project.urls]
Homepage = "https://github.com/username/hypercalc"
Documentation = "https://hypercalc.readthedocs.io"
Repository = "https://github.com/username/hypercalc.git"
"Issue Tracker" = "https://github.com/username/hypercalc/issues"
```

---

## Building the Package Artifacts

To create distributable `.whl` and `.tar.gz` files, use the official `build` frontend:

```bash
# 1. Install build tool
python -m pip install build

# 2. Build distributions
python -m build
```

This generates two artifacts inside a newly created `dist/` directory:
- `dist/hypercalc-1.0.0-py3-none-any.whl` (The fast pre-built binary wheel)
- `dist/hypercalc-1.0.0.tar.gz` (The source distribution archive)

---

## Checking Package Integrity with `twine`

Before publishing to PyPI, verify that your metadata and README render correctly:

```bash
# Install twine
python -m pip install twine

# Check built artifacts for syntax errors or bad metadata
twine check dist/*
```

<div class="terminal-output">
Checking dist/hypercalc-1.0.0-py3-none-any.whl: PASSED
Checking dist/hypercalc-1.0.0.tar.gz: PASSED
</div>

---

## Publishing to PyPI

### 1. Test First on TestPyPI

Always upload to the TestPyPI staging environment first to verify appearance and test installation:

```bash
# Upload to TestPyPI
python -m twine upload --repository testpypi dist/*
```

Test installing your package from TestPyPI into a clean virtual environment:

```bash
python -m pip install --index-url https://test.pypi.org/simple/ --no-deps hypercalc
```

### 2. Publish to Official PyPI

Once verified, upload to the production PyPI:

```bash
python -m twine upload dist/*
```

Enter your PyPI API token (`pypi-...`) when prompted for credentials. Within seconds, your library is globally installable by any Python developer worldwide via `pip install hypercalc`.
