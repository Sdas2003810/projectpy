# Extending Python with C or C++

Python is designed to be easily extensible. You can write compiled C or C++ functions that can be imported and executed in Python just like any normal Python module.

Extending Python allows you to:
- Accelerate performance-critical bottlenecks by 50x to 100x.
- Integrate with hardware drivers, proprietary C SDKs, and legacy libraries.
- Implement low-level memory manipulations or system calls.

---

## Anatomy of a C Extension Module

Every C extension module includes four essential components:

1. **Header Inclusion**: `#define PY_SSIZE_T_CLEAN` followed by `#include <Python.h>`.
2. **C Wrapper Functions**: Functions that accept `PyObject*` arguments and return a `PyObject*`.
3. **Method Table**: An array of `PyMethodDef` structures describing the module's functions to Python.
4. **Module Initialization Function**: The `PyMODINIT_FUNC PyInit_<modulename>(void)` entry point.

### Complete Example: `fastmath.c`

```c
#define PY_SSIZE_T_CLEAN
#include <Python.h>

// 1. C Implementation of a fast fibonacci function
static long long c_fib(long long n) {
    if (n <= 1) return n;
    long long a = 0, b = 1;
    for (long long i = 2; i <= n; i++) {
        long long temp = a + b;
        a = b;
        b = temp;
    }
    return b;
}

// 2. Python wrapper function
static PyObject* fastmath_fib(PyObject* self, PyObject* args) {
    long long n;

    // Parse Python integer argument into C long long
    if (!PyArg_ParseTuple(args, "L", &n)) {
        return NULL; // Exception already set by PyArg_ParseTuple
    }

    if (n < 0) {
        PyErr_SetString(PyExc_ValueError, "n must be a non-negative integer");
        return NULL;
    }

    long long result = c_fib(n);

    // Convert C long long back into a Python int object
    return PyLong_FromLongLong(result);
}

// 3. Module Method Table
static PyMethodDef FastMathMethods[] = {
    {"fib", fastmath_fib, METH_VARARGS, "Compute fibonacci in fast C."},
    {NULL, NULL, 0, NULL} // Sentinel marking end of array
};

// 4. Module Definition Struct
static struct PyModuleDef fastmathmodule = {
    PyModuleDef_HEAD_INIT,
    "fastmath",                                 // Module name
    "A high-performance C extension module.",  // Docstring
    -1,                                        // Global state size (-1 = no sub-interpreter state)
    FastMathMethods
};

// 5. Module Initialization Entry Point
PyMODINIT_FUNC PyInit_fastmath(void) {
    return PyModule_Create(&fastmathmodule);
}
```

---

## Parsing Arguments: `PyArg_ParseTuple`

The `PyArg_ParseTuple()` function converts Python objects into native C data types:

| Format Specifier | Python Type | C Variable Type |
| :--- | :--- | :--- |
| `"i"` | `int` | `int` |
| `"l"` | `int` | `long` |
| `"L"` | `int` | `long long` |
| `"f"` | `float` | `float` |
| `"d"` | `float` | `double` |
| `"s"` | `str` | `const char*` (UTF-8 encoded string) |
| `"O"` | Any | `PyObject*` (Raw Python object pointer) |
| `"O!"` | Specific class | Class pointer + `PyObject*` |

---

## Reference Counting Rules

CPython uses reference counting for garbage collection. Managing references correctly in C extensions is essential to prevent memory leaks and crashes:

- **Owned Reference**: Your C function owns the object. When you are done with it, you must call `Py_DECREF(obj)`. If an owned reference leaks, memory remains uncollected.
- **Borrowed Reference**: Your function is merely inspecting the object; you do not own it. If you need to keep it across function calls, you must explicitly call `Py_INCREF(obj)`.

```c
PyObject* list = PyList_New(2); // Returns a new owned reference

PyObject* item = PyLong_FromLong(42); // New owned reference
PyList_SetItem(list, 0, item); // Steals the reference to item! (No DECREF needed for item)

return list; // Caller now owns list
```

---

## Returning None in C

To return `None` from a C extension function:

```c
Py_RETURN_NONE; // Macro that increments None's refcount and returns Py_None
```
