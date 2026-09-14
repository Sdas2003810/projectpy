# Building & Compiling C Extensions

Once you have written a C extension module (such as `fastmath.c`), you must compile it into a shared library (`.so` on Linux, `.dylib` on macOS, or `.pyd` on Windows) that Python can import.

---

## Compiler Prerequisites

To compile C code, your system needs an appropriate C compiler:

- **Windows**: Install the **Visual Studio C++ Build Tools** (select "Desktop development with C++").
- **macOS**: Install the Xcode Command Line Tools:
  ```bash
  xcode-select --install
  ```
- **Linux (Ubuntu/Debian)**: Install `build-essential` and `python3-dev`:
  ```bash
  sudo apt install build-essential python3-dev
  ```

---

## Building with `setuptools` and `setup.py`

While modern pure-Python packaging uses declarative `pyproject.toml`, compiled C extensions use `setuptools.Extension`:

```python
# setup.py
from setuptools import setup, Extension

# Define the C extension module
fastmath_module = Extension(
    name="fastmath",
    sources=["src/fastmath.c"],
    include_dirs=["include"],
    extra_compile_args=["-O3"]  # Enable compiler optimizations
)

setup(
    name="fastmath",
    version="1.0.0",
    description="Optimized C extension module",
    ext_modules=[fastmath_module]
)
```

Pair this with `pyproject.toml`:

```toml
[build-system]
requires = ["setuptools>=61.0", "wheel"]
build-backend = "setuptools.build_meta"
```

---

## Compiling for Local Development: `--inplace`

To compile the C source code and place the resulting `.so` or `.pyd` directly into your working directory for immediate testing:

```bash
python setup.py build_ext --inplace
```

You can immediately open a Python REPL in the same folder and test your module:

```python
import fastmath
print(fastmath.fib(35))
```

---

## Automated Multi-Platform Wheels with `cibuildwheel`

Compiling C extensions for PyPI is challenging because Linux distributions require standard **manylinux** glibc compatibility, macOS requires Universal2 (Intel + Apple Silicon) architectures, and Windows requires MSVC runtimes.

The Python Packaging Authority provides **`cibuildwheel`**, which compiles binary wheels inside Docker containers in GitHub Actions:

```yaml
# .github/workflows/wheels.yml
name: Build Wheels
on: [push, pull_request]

jobs:
  build_wheels:
    name: Build wheels on ${{ matrix.os }}
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]

    steps:
      - uses: actions/checkout@v4
      - name: Build wheels
        uses: pypa/cibuildwheel@v2.18.0
      - uses: actions/upload-artifact@v4
        with:
          name: cibw-wheels-${{ matrix.os }}
          path: ./wheelhouse/*.whl
```

Running `cibuildwheel` generates fully compliant, self-contained `.whl` files ready for distribution to end-users without requiring them to have C compilers.

---

## Modern Next-Gen Build Backends

For modern multi-language projects:
- **`scikit-build-core`**: High-performance CMake-based build system for C, C++, and CUDA.
- **`meson-python`**: Lightning-fast Meson build backend (used by SciPy and NumPy).
- **`maturin`**: Zero-configuration packaging backend for compiling **Rust** extensions via PyO3.
