# Installing Python Modules & Using `pip`

Python features an enormous ecosystem of open-source third-party libraries available on the **Python Package Index (PyPI)**. The standard tool for installing and managing these libraries is **`pip`**.

---

## The Standard Installation Command

Always invoke `pip` using the `python -m pip` syntax to ensure packages are installed into the active Python environment:

```bash
python -m pip install requests
```

To install a specific version or range:

```bash
# Exact version
python -m pip install requests==2.31.0

# Minimum version
python -m pip install "fastapi>=0.100.0"

# Compatible release (matches >= 1.4.0, < 2.0.0)
python -m pip install "pydantic~=1.4"
```

---

## Managing Project Dependencies with `requirements.txt`

For reproducible deployments and team development, record all project dependencies in a `requirements.txt` file:

```txt
# requirements.txt
fastapi>=0.110.0
uvicorn[standard]>=0.28.0
sqlalchemy>=2.0.0
pydantic>=2.6.0
python-dotenv>=1.0.0
```

Install all listed dependencies in one command:

```bash
python -m pip install -r requirements.txt
```

### Freezing Exact Pinned Versions

To create an exact snapshot of every installed package (including transitive sub-dependencies):

```bash
python -m pip freeze > requirements.lock
```

---

## Wheels (`.whl`) vs Source Distributions (`sdist`)

When `pip` downloads a package, it looks for two formats:

```
               PyPI Package Artifacts
                      |
        +-------------+-------------+
        |                           |
        v                           v
   Wheel (.whl)            Source Dist (.tar.gz)
   - Pre-built binary      - Raw Python & C source code
   - No compiler required  - Requires gcc/clang/MSVC
   - Installs in ms        - Takes minutes to compile
```

- **Wheel (`.whl`)**: A pre-compiled binary package. It requires no C compilers or external header files on your machine. Packages like `numpy`, `cryptography`, and `pandas` install instantly.
- **Source Distribution (`.tar.gz` / `sdist`)**: Contains raw source code. If the package contains C or Rust extensions, your computer must have the appropriate compiler tools installed.

---

## Editable Installs for Local Development (`-e`)

When developing your own Python package or library, you want your code changes to take effect immediately without having to run `pip install` after every edit.

Use **editable mode** (`-e`):

```bash
# Inside the root of your package repo (where pyproject.toml lives)
python -m pip install -e .
```

This adds a direct link (via a `.pth` file) into `site-packages` pointing to your local repository directory.

---

## Installing from Git Repositories

You can install packages directly from GitHub or GitLab branches or commit hashes:

```bash
python -m pip install git+https://github.com/psf/requests.git@main
```

---

## Upgrading and Uninstalling

```bash
# Upgrade an existing package to the latest release
python -m pip install --upgrade requests

# Uninstall a package
python -m pip uninstall requests

# Inspect package metadata and install location
python -m pip show requests
```
