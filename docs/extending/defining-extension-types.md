# Defining Custom Extension Types in C

While creating simple C functions is straightforward, defining a custom Python class (an **extension type**) in C gives you complete control over memory layout, attribute lookups, and method dispatch.

---

## The Object Struct and `PyObject_HEAD`

All Python objects in C must begin with the `PyObject_HEAD` macro, which contains the reference count (`ob_refcnt`) and type pointer (`ob_type`):

```c
#define PY_SSIZE_T_CLEAN
#include <Python.h>
#include "structmember.h"

// Definition of our custom C object
typedef struct {
    PyObject_HEAD
    PyObject *first_name; // Managed Python string
    PyObject *last_name;  // Managed Python string
    int age;              // Raw C integer (no Python object overhead!)
} CustomObject;
```

---

## Memory Lifecycle: `tp_new`, `tp_init`, and `tp_dealloc`

### 1. `tp_new` (Allocation)

Allocates raw memory for the object:

```c
static PyObject *Custom_new(PyTypeObject *type, PyObject *args, PyObject *kwds) {
    CustomObject *self;
    self = (CustomObject *)type->tp_alloc(type, 0);
    if (self != NULL) {
        self->first_name = PyUnicode_FromString("");
        if (self->first_name == NULL) {
            Py_DECREF(self);
            return NULL;
        }
        self->last_name = PyUnicode_FromString("");
        if (self->last_name == NULL) {
            Py_DECREF(self);
            return NULL;
        }
        self->age = 0;
    }
    return (PyObject *)self;
}
```

### 2. `tp_init` (Initialization)

Initializes object attributes (equivalent to Python's `__init__`):

```c
static int Custom_init(CustomObject *self, PyObject *args, PyObject *kwds) {
    static char *kwlist[] = {"first", "last", "age", NULL};
    PyObject *first = NULL, *last = NULL;

    if (!PyArg_ParseTupleAndKeywords(args, kwds, "|SSi", kwlist, &first, &last, &self->age)) {
        return -1;
    }

    if (first) {
        Py_INCREF(first);
        Py_DECREF(self->first_name);
        self->first_name = first;
    }
    if (last) {
        Py_INCREF(last);
        Py_DECREF(self->last_name);
        self->last_name = last;
    }
    return 0;
}
```

### 3. `tp_dealloc` (Cleanup)

Frees memory when the reference count drops to zero:

```c
static void Custom_dealloc(CustomObject *self) {
    Py_XDECREF(self->first_name);
    Py_XDECREF(self->last_name);
    Py_TYPE(self)->tp_free((PyObject *)self);
}
```

---

## Exposing Fields to Python with `PyMemberDef`

To allow Python code to read and write struct members (e.g. `c.age = 30`), define an attribute table:

```c
static PyMemberDef Custom_members[] = {
    {"first", T_OBJECT_EX, offsetof(CustomObject, first_name), 0, "First name"},
    {"last", T_OBJECT_EX, offsetof(CustomObject, last_name), 0, "Last name"},
    {"age", T_INT, offsetof(CustomObject, age), 0, "Age in years"},
    {NULL} /* Sentinel */
};
```

---

## The Type Object Specification: `PyTypeObject`

```c
static PyTypeObject CustomType = {
    PyVarObject_HEAD_INIT(NULL, 0)
    .tp_name = "custom.Custom",
    .tp_doc = PyDoc_STR("Custom object implemented in C"),
    .tp_basicsize = sizeof(CustomObject),
    .tp_itemsize = 0,
    .tp_flags = Py_TPFLAGS_DEFAULT | Py_TPFLAGS_BASETYPE,
    .tp_new = Custom_new,
    .tp_init = (initproc)Custom_init,
    .tp_dealloc = (destructor)Custom_dealloc,
    .tp_members = Custom_members,
};
```

---

## Module Registration

In `PyInit_custom()`:

```c
PyMODINIT_FUNC PyInit_custom(void) {
    PyObject *m;
    if (PyType_Ready(&CustomType) < 0)
        return NULL;

    m = PyModule_Create(&custommodule);
    if (m == NULL)
        return NULL;

    Py_INCREF(&CustomType);
    if (PyModule_AddObject(m, "Custom", (PyObject *)&CustomType) < 0) {
        Py_DECREF(&CustomType);
        Py_DECREF(m);
        return NULL;
    }
    return m;
}
```

Now from Python:

```python
import custom

c = custom.Custom(first="Grace", last="Hopper", age=85)
print(c.first)  # "Grace"
print(c.age)    # 85
```
