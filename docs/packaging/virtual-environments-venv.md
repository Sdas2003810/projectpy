# Virtual Environments in Packaging & Testing Workflows

While virtual environments are commonly used by application developers for daily coding, they serve an equally critical purpose in the **packaging and distribution ecosystem**.

Packaging tools (such as `build`, `pip`, `tox`, and `nox`) rely on temporary, ephemeral virtual environments to ensure deterministic builds, test across multiple Python matrix versions, and prevent cross-contamination.

---

## Isolated Build Environments (PEP 517)

When you run `python -m build`, you might notice it creates a temporary environment behind the scenes:

```
* Creating venv isolated environment...
* Installing build-backend dependencies (hatchling)...
* Getting dependencies for wheel...
* Building wheel...
* Successfully built hypercalc-1.0.0-py3-none-any.whl
```

### Why Build Isolation Matters

Before PEP 517, building a package ran `setup.py` using whatever packages were installed in your current global environment. If you had an unpinned experimental version of a compiler or build tool installed, the generated wheel would be corrupted for other users.

With build isolation:
1. `build` creates a fresh, clean virtual environment in a temporary folder.
2. It inspects `pyproject.toml`'s `[build-system] requires` section and installs only the declared build dependencies.
3. It compiles the wheel inside that sterile environment.
4. It deletes the temporary virtual environment upon completion.

To disable isolation during local debugging:

```bash
python -m build --no-build-isolation
```

---

## Matrix Testing Across Python Versions with `tox` & `nox`

When distributing an open-source library on PyPI, you must verify that your code works across all supported Python versions (e.g., Python 3.10, 3.11, 3.12, 3.13).

### Using `tox`

`tox` automates creating separate virtual environments for each target Python version and executing your test suite:

```ini
# tox.ini
[tox]
envlist = py310, py311, py312, py313

[testenv]
deps =
    pytest
    coverage
commands =
    pytest
```

Running `tox` in your terminal:
- Spawns `.tox/py310`, installs dependencies, and runs tests.
- Spawns `.tox/py311`, installs dependencies, and runs tests.
- Summarizes pass/fail status across the entire matrix.

---

## Packaging Executables with Bundled Environments: `shiv` & `pex`

When distributing Python CLI utilities to end-users who do not want to manage Python virtual environments, you can bundle the entire virtual environment into a single executable zip file:

```bash
# Install shiv
pip install shiv

# Pack an entire virtual environment and entrypoint into a single binary file:
shiv -c hypercalc -o hypercalc.bin requests click .

# Now runnable on any machine with Python installed, zero pip install needed:
./hypercalc.bin --help
```
