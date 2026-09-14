# CPython C-API Overview

The **Python/C API** allows C and C++ programmers to interact with the Python runtime at the deepest level:
1. **Extending**: Writing compiled C modules that Python code can import.
2. **Embedding**: Embedding the Python interpreter inside a larger C/C++ application (such as game engines, CAD suites, or trading platforms).

---

## Architectural Layers of the C-API

The C-API is structured into three distinct layers of abstraction:

```
+-------------------------------------------------------------+
| 1. Very High-Level Layer                                    |
|    Simple execution helpers (PyRun_SimpleString, PyRun_File)|
+-------------------------------------------------------------+
| 2. Abstract Object Layer                                    |
|    Polymorphic protocols (PyObject_*, PySequence_*,         |
|    PyMapping_*, PyNumber_*)                                  |
+-------------------------------------------------------------+
| 3. Concrete Object Layer                                    |
|    Type-specific APIs (PyLong_*, PyUnicode_*, PyList_*,      |
|    PyDict_*, PyTuple_*)                                     |
+-------------------------------------------------------------+
```

---

## Embedding Python in a C Application

To run Python scripts directly inside an existing C application:

```c
#define PY_SSIZE_T_CLEAN
#include <Python.h>

int main(int argc, char *argv[]) {
    // 1. Initialize the Python interpreter runtime
    Py_Initialize();

    // 2. Execute Python code directly
    PyRun_SimpleString(
        "import sys\n"
        "print(f'Hello from embedded Python {sys.version_info.major}.{sys.version_info.minor}!')\n"
        "total = sum(x**2 for x in range(10))\n"
        "print(f'Sum of squares: {total}')\n"
    );

    // 3. Cleanly shut down the Python runtime and free memory
    if (Py_FinalizeEx() < 0) {
        return 120;
    }

    return 0;
}
```

---

## Managing the GIL in C Extensions

When your C code executes a time-consuming operation (such as processing an image, running numerical simulations, or waiting on network sockets), **you should release the Global Interpreter Lock (GIL)** to allow other Python threads to execute in parallel:

```c
// Thread currently holds the GIL

Py_BEGIN_ALLOW_THREADS
    // GIL is released here!
    // Native C threads in Python can now run simultaneously on other CPU cores.
    // WARNING: You CANNOT manipulate any PyObject* pointers inside this block!
    compute_heavy_c_simulation(data_buffer, 1000000);
Py_END_ALLOW_THREADS

// GIL is re-acquired here. Safe to create/manipulate PyObjects again.
return PyLong_FromLong(1);
```

### Acquiring the GIL from C Callback Threads

If an external C background thread (created via `pthread_create` or Windows `CreateThread`) needs to invoke a Python function, it must first acquire the GIL:

```c
PyGILState_STATE gstate;
gstate = PyGILState_Ensure();

// Call Python API safely
PyObject_CallObject(python_callback, NULL);

// Release GIL and restore thread state
PyGILState_Release(gstate);
```

---

## The Limited API & Stable ABI (`abi3`) (PEP 384)

By default, Python C extension modules are tightly coupled to the exact minor version of CPython they were compiled against. A wheel built for Python 3.11 will crash or refuse to load on Python 3.12!

### The Solution: `#define Py_LIMITED_API`

PEP 384 introduced the **Limited API** and **Stable ABI**:
- Restricts your C code to a curated subset of CPython APIs guaranteed to never change internal struct layouts or binary calling conventions across minor releases.
- Compiles into an **`abi3`** wheel (e.g. `fastmath-1.0.0-cp38-abi3-manylinux.whl`).

```c
#define Py_LIMITED_API 0x03080000 // Target Python 3.8+ Stable ABI
#define PY_SSIZE_T_CLEAN
#include <Python.h>
```

A single wheel compiled with `abi3` for Python 3.8 **runs without modification** across Python 3.8, 3.9, 3.10, 3.11, 3.12, 3.13, and 3.14!
