# Metaclasses in Python

In Python, everything is an object. Numbers are objects, strings are objects, functions are objects, and classes themselves are objects. And just as normal objects are instances created from a class, classes are instances created from a **metaclass**.

Tim Peters, author of *The Zen of Python*, famously wrote:
> *"Metaclasses are deeper magic than 99% of users should ever worry about. If you wonder whether you need them, you don't."*

While that advice holds true for everyday programming, understanding metaclasses unlocks the deepest layer of Python's object model and explains how frameworks like Django, SQLAlchemy, and Pydantic build their declarative class architectures.

---

## The Default Metaclass: `type`

Most programmers know `type()` as a built-in function to inspect the type of an object:

```python
print(type(42))         # <class 'int'>
print(type("hello"))    # <class 'str'>
```

However, `type` is actually a class — specifically, the default metaclass in Python. When you inspect the type of a class, Python reveals that `type` is its creator:

```python
class Dog:
    pass

print(type(Dog))        # <class 'type'>
print(isinstance(Dog, type))  # True
```

### Dynamic Class Creation with `type()`

`type` has a dual nature. When called with three arguments, it dynamically creates a new class at runtime:

```python
type(name, bases, namespace_dict)
```

| Argument | Description | Example |
| :--- | :--- | :--- |
| `name` | The name of the class to create (string) | `"User"` |
| `bases` | A tuple of base classes to inherit from | `(object,)` |
| `namespace_dict` | A dictionary containing attributes and methods | `{"role": "admin", "login": func}` |

Compare standard class definition with dynamic generation:

=== "Standard Class Definition"
    ```python
    class Robot:
        species = "Automaton"

        def speak(self):
            return f"Hello, I am an {self.species}."
    ```

=== "Dynamic Creation with `type`"
    ```python
    def speak_method(self):
        return f"Hello, I am an {self.species}."

    Robot = type(
        "Robot",
        (object,),
        {"species": "Automaton", "speak": speak_method}
    )
    ```

Both create identical class objects with full inheritance, methods, and attribute lookups.

---

## How Python Creates a Class

When Python encounters a `class` statement, it executes a four-step lifecycle:

```
1. Resolve Metaclass
   Look for `metaclass=...`, or inherit from base classes, or default to `type`.
      |
      v
2. Prepare Class Namespace
   Call `metaclass.__prepare__(name, bases, **kwargs)` to create the mapping (default: dict).
      |
      v
3. Execute Class Body
   Run the code inside the class body, populating the prepared namespace.
      |
      v
4. Instantiate the Class
   Call `metaclass(name, bases, namespace, **kwargs)`:
   -> Calls `metaclass.__new__()` to allocate the class object
   -> Calls `metaclass.__init__()` to initialize the class object
```

---

## Creating a Custom Metaclass

To define a custom metaclass, inherit from `type` and override `__new__`:

```python
class EnforceUppercaseMeta(type):
    def __new__(mcs, name, bases, namespace):
        # Inspect and transform class attributes
        transformed_namespace = {}
        for attr_name, attr_val in namespace.items():
            if not attr_name.startswith("__") and not callable(attr_val):
                transformed_namespace[attr_name.upper()] = attr_val
            else:
                transformed_namespace[attr_name] = attr_val
        
        # Call parent type.__new__ to create the class
        return super().__new__(mcs, name, bases, transformed_namespace)

class Config(metaclass=EnforceUppercaseMeta):
    port = 8080
    host = "localhost"
    timeout = 30

print(hasattr(Config, "PORT"))     # True
print(hasattr(Config, "port"))     # False
print(Config.PORT)                 # 8080
```

### Parameters of `__new__` in a Metaclass

- `mcs`: The metaclass itself (`EnforceUppercaseMeta`).
- `name`: The name of the class being created as a string (e.g., `"Config"`).
- `bases`: A tuple of base classes specified in the class definition.
- `namespace`: A dictionary containing all attributes, methods, and docstrings defined in the class body.

---

## Real-World Pattern: Automatic Plugin Registry

A classic use-case for metaclasses is building an automatic registry of plugins without requiring manual registration:

```python
class PluginRegistryMeta(type):
    plugins = {}

    def __new__(mcs, name, bases, namespace):
        cls = super().__new__(mcs, name, bases, namespace)
        
        # Don't register the abstract base plugin class itself
        plugin_name = namespace.get("plugin_name")
        if plugin_name:
            mcs.plugins[plugin_name] = cls
            
        return cls

class BasePlugin(metaclass=PluginRegistryMeta):
    plugin_name = None

    def execute(self):
        raise NotImplementedError

class JsonPlugin(BasePlugin):
    plugin_name = "json"

    def execute(self):
        return "Parsing JSON data..."

class CsvPlugin(BasePlugin):
    plugin_name = "csv"

    def execute(self):
        return "Parsing CSV data..."

# Usage: plugins are automatically discovered and registered
print(PluginRegistryMeta.plugins)
# {'json': <class '__main__.JsonPlugin'>, 'csv': <class '__main__.CsvPlugin'>}

def run_parser(format_type: str):
    plugin_cls = PluginRegistryMeta.plugins.get(format_type)
    if not plugin_cls:
        raise ValueError(f"Unknown plugin format: {format_type}")
    return plugin_cls().execute()

print(run_parser("json"))
```

<div class="terminal-output">
Parsing JSON data...
</div>

---

## Modern Alternatives: `__init_subclass__`

In modern Python (Python 3.6+ / PEP 487), you rarely need a full metaclass for registration, attribute validation, or inheritance hooks. Python provides the `__init_subclass__` hook:

```python
class BaseService:
    subclasses = {}

    def __init_subclass__(cls, service_name: str, **kwargs):
        super().__init_subclass__(**kwargs)
        cls.subclasses[service_name] = cls

class EmailService(BaseService, service_name="email"):
    pass

class SMSService(BaseService, service_name="sms"):
    pass

print(BaseService.subclasses)
# {'email': <class '__main__.EmailService'>, 'sms': <class '__main__.SMSService'>}
```

### When to Use Which?

| Feature | `__init_subclass__` | Class Decorator | Metaclass |
| :--- | :--- | :--- | :--- |
| **Complexity** | Low | Low | High |
| **Inherited by child classes?** | Yes | No (only decorates target) | Yes |
| **Can modify class namespace before creation?** | No | No | Yes (via `__prepare__`) |
| **Can replace the class object entirely?** | No | Yes | Yes |
| **Metaclass conflicts possible?** | No | No | Yes |

!!! tip "Guideline for Modern Python"
    Always check if your problem can be solved with **`__init_subclass__`** or a **class decorator** first. Reserve metaclasses exclusively for cases where you must alter the class namespace creation (`__prepare__`) or customize attribute lookup on the class object itself.

---

## Metaclass Conflict and Resolution

If a class inherits from multiple base classes whose metaclasses are not subclasses of each other, Python raises a `TypeError`:

```python
class MetaA(type): pass
class MetaB(type): pass

class ClassA(metaclass=MetaA): pass
class ClassB(metaclass=MetaB): pass

# Raises TypeError: metaclass conflict:
# the metaclass of a derived class must be a (non-strict) subclass
# of the metaclasses of all its bases
# class Combined(ClassA, ClassB): pass
```

To resolve this conflict, create a composite metaclass inheriting from both:

```python
class ResolvedMeta(MetaA, MetaB): pass

class Combined(ClassA, ClassB, metaclass=ResolvedMeta):
    pass
```
