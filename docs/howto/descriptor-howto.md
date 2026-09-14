# Descriptor Guide & Reference HOWTO

Written in the tradition of Raymond Hettinger's classic Python descriptor guide, this HOWTO explores the exact mechanics of Python's descriptor protocol, showing how standard features like `@property`, regular methods, `@classmethod`, and `@staticmethod` are implemented internally.

---

## The Core Invocation Mechanism

The entry point for attribute access is `object.__getattribute__()`. When you evaluate `obj.x`, Python transforms the lookup into:

```python
# Conceptual implementation of object.__getattribute__(obj, 'x'):
def find_attribute(obj, name):
    cls = type(obj)
    
    # 1. Search class and MRO for descriptor
    descr = getattr(cls, name, None)
    
    # 2. Data descriptor? (has __set__ or __delete__)
    if descr is not None and hasattr(type(descr), "__set__"):
        return type(descr).__get__(descr, obj, cls)
        
    # 3. Check instance dict
    if name in obj.__dict__:
        return obj.__dict__[name]
        
    # 4. Non-data descriptor? (has __get__ only)
    if descr is not None and hasattr(type(descr), "__get__"):
        return type(descr).__get__(descr, obj, cls)
        
    # 5. Fallback to class attribute
    if descr is not None:
        return descr
        
    # 6. Fallback to __getattr__ if defined
    if hasattr(cls, "__getattr__"):
        return cls.__getattr__(obj, name)
        
    raise AttributeError(f"'{cls.__name__}' object has no attribute '{name}'")
```

---

## Re-implementing Python Built-ins as Pure Descriptors

Seeing how Python's built-in decorators are constructed in pure Python clarifies the power of descriptors:

### 1. Pure Python `property`

```python
class Property:
    "Emulate PyProperty_Type() in Objects/descrobject.c"

    def __init__(self, fget=None, fset=None, fdel=None, doc=None):
        self.fget = fget
        self.fset = fset
        self.fdel = fdel
        if doc is None and fget is not None:
            doc = fget.__doc__
        self.__doc__ = doc

    def __get__(self, obj, objtype=None):
        if obj is None:
            return self
        if self.fget is None:
            raise AttributeError("unreadable attribute")
        return self.fget(obj)

    def __set__(self, obj, value):
        if self.fset is None:
            raise AttributeError("can't set attribute")
        self.fset(obj, value)

    def __delete__(self, obj):
        if self.fdel is None:
            raise AttributeError("can't delete attribute")
        self.fdel(obj)

    def setter(self, fset):
        return type(self)(self.fget, fset, self.fdel, self.__doc__)
```

### 2. Pure Python `staticmethod`

A static method simply returns the underlying function directly without binding `self` or `cls`:

```python
class StaticMethod:
    "Emulate PyStaticMethod_Type() in Objects/funcobject.c"

    def __init__(self, f):
        self.f = f

    def __get__(self, obj, objtype=None):
        return self.f

    def __call__(self, *args, **kwargs):
        return self.f(*args, **kwargs)
```

### 3. Pure Python `classmethod`

A class method binds the containing class (`cls`) as the first argument:

```python
import functools

class ClassMethod:
    "Emulate PyClassMethod_Type() in Objects/funcobject.c"

    def __init__(self, f):
        self.f = f

    def __get__(self, obj, cls=None):
        if cls is None:
            cls = type(obj)
        return functools.partial(self.f, cls)
```

---

## Building a Real-World Validation Framework

Descriptors shine when building type-safe schema definitions:

```python
from abc import ABC, abstractmethod

class Validator(ABC):
    def __set_name__(self, owner, name):
        self.private_name = f"_{name}"

    def __get__(self, obj, objtype=None):
        if obj is None:
            return self
        return getattr(obj, self.private_name)

    def __set__(self, obj, value):
        self.validate(value)
        setattr(obj, self.private_name, value)

    @abstractmethod
    def validate(self, value):
        pass

class Number(Validator):
    def __init__(self, minvalue=None, maxvalue=None):
        self.minvalue = minvalue
        self.maxvalue = maxvalue

    def validate(self, value):
        if not isinstance(value, (int, float)):
            raise TypeError(f"Expected {value!r} to be an int or float")
        if self.minvalue is not None and value < self.minvalue:
            raise ValueError(f"Expected {value!r} to be >= {self.minvalue}")
        if self.maxvalue is not None and value > self.maxvalue:
            raise ValueError(f"Expected {value!r} to be <= {self.maxvalue}")

class String(Validator):
    def __init__(self, minsize=1, maxsize=100):
        self.minsize = minsize
        self.maxsize = maxsize

    def validate(self, value):
        if not isinstance(value, str):
            raise TypeError(f"Expected {value!r} to be a str")
        if len(value) < self.minsize:
            raise ValueError(f"Length must be >= {self.minsize}")
```

Using the validation descriptors:

```python
class Product:
    title = String(minsize=3, maxsize=50)
    price = Number(minvalue=0.01)

    def __init__(self, title: str, price: float):
        self.title = title
        self.price = price

# Valid product
laptop = Product("UltraBook Pro", 1299.99)

# Invalid price raises ValueError automatically:
try:
    laptop.price = -50.0
except ValueError as e:
    print(f"Validation error caught: {e}")
```

<div class="terminal-output">
Validation error caught: Expected -50.0 to be >= 0.01
</div>
