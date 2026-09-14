# Complete Python Built-in Functions Reference

The Python interpreter has a number of functions and types built into it that are always available without importing any module. Below is the complete reference table of all built-in functions in the latest Python release.

---

## Built-in Functions Lookup Table

| Function | Description | Example |
| :--- | :--- | :--- |
| `abs(x)` | Returns the absolute value of a number | `abs(-7.5) -> 7.5` |
| `aiter(async_iterable)` | Returns an asynchronous iterator for an asynchronous iterable (Python 3.10+) | `aiter(async_gen)` |
| `all(iterable)` | Returns `True` if all elements of the iterable are true (or if empty) | `all([True, 1, "yes"]) -> True` |
| `anext(async_iterator)` | Returns the next item from the given asynchronous iterator (Python 3.10+) | `await anext(ait)` |
| `any(iterable)` | Returns `True` if any element of the iterable is true | `any([0, False, 5]) -> True` |
| `ascii(object)` | Returns a string containing a printable representation with non-ASCII escaped | `ascii('pi: π') -> "'pi: \\u03c0'"` |
| `bin(x)` | Converts an integer to a binary string prefixed with `0b` | `bin(10) -> '0b1010'` |
| `bool(x)` | Converts a value to a Boolean (`True` or `False`) | `bool(0) -> False` |
| `breakpoint(*args, **kws)` | Drops into the debugger at the call site (triggers `sys.breakpointhook`) | `breakpoint()` |
| `bytearray([source])` | Returns a new mutable array of bytes | `bytearray(b'abc')` |
| `bytes([source])` | Returns a new immutable bytes object | `bytes([65, 66]) -> b'AB'` |
| `callable(object)` | Returns `True` if the object argument appears callable (function, class, etc.) | `callable(len) -> True` |
| `chr(i)` | Returns the string representing a character whose Unicode code point is integer $i$ | `chr(97) -> 'a'` |
| `classmethod(function)` | Converts a function into a class method | `@classmethod` |
| `compile(source, filename, mode)` | Compiles the source into a code or AST object for `exec()` or `eval()` | `compile('a + 1', '', 'eval')` |
| `complex([real[, imag]])` | Returns a complex number with the value $real + imag \cdot j$ | `complex(2, 3) -> (2+3j)` |
| `delattr(object, name)` | Deletes the named attribute from an object | `delattr(person, 'age')` |
| `dict(**kwarg)` | Creates a new dictionary | `dict(a=1, b=2)` |
| `dir([object])` | Returns list of valid attributes for the object (or local scope if omitted) | `dir([])` |
| `divmod(a, b)` | Returns a pair `(a // b, a % b)` using integer division | `divmod(17, 5) -> (3, 2)` |
| `enumerate(iterable, start=0)` | Returns an enumerate object yielding `(index, item)` pairs | `enumerate(['a', 'b'])` |
| `eval(expression[, globals[, locals]])` | Parses and evaluates an expression string | `eval('2 + 3 * 4') -> 14` |
| `exec(object[, globals[, locals]])` | Dynamically executes Python code statements | `exec('x = 5')` |
| `filter(function, iterable)` | Constructs an iterator from elements where function returns true | `filter(lambda x: x > 0, nums)` |
| `float([x])` | Converts a number or string to a floating-point number | `float("3.14") -> 3.14` |
| `format(value[, format_spec])` | Formats a value according to the format specifier | `format(1234, ',') -> '1,234'` |
| `frozenset([iterable])` | Returns a new immutable set object | `frozenset([1, 2, 3])` |
| `getattr(object, name[, default])` | Returns the value of the named attribute of an object | `getattr(user, 'name', 'Guest')` |
| `globals()` | Returns the dictionary implementing the current module namespace | `globals()` |
| `hasattr(object, name)` | Returns `True` if string is the name of one of the object's attributes | `hasattr(user, 'email')` |
| `hash(object)` | Returns the hash value of the object (if hashable) | `hash("hello")` |
| `help([object])` | Invokes the built-in help system | `help(str.split)` |
| `hex(x)` | Converts an integer to a lowercase hexadecimal string prefixed with `0x` | `hex(255) -> '0xff'` |
| `id(object)` | Returns the identity (memory address integer) of an object | `id(var)` |
| `input([prompt])` | Reads a line from input, converts it to a string, and returns it | `name = input("Enter name: ")` |
| `int([x[, base]])` | Converts a number or string to an integer | `int("101", 2) -> 5` |
| `isinstance(object, classinfo)` | Returns `True` if the object is an instance or subclass of classinfo | `isinstance(42, int) -> True` |
| `issubclass(class, classinfo)` | Returns `True` if class is a subclass of classinfo | `issubclass(bool, int) -> True` |
| `iter(object[, sentinel])` | Returns an iterator object | `iter([1, 2, 3])` |
| `len(s)` | Returns the length (the number of items) of an object | `len("Python") -> 6` |
| `list([iterable])` | Creates a new list | `list((1, 2, 3)) -> [1, 2, 3]` |
| `locals()` | Updates and returns a dictionary representing the current local symbol table | `locals()` |
| `map(function, iterable, ...)` | Returns an iterator that applies function to every item of iterable | `map(abs, [-1, -2, 3])` |
| `max(iterable, *[, key, default])` | Returns the largest item in an iterable or among two or more arguments | `max(5, 9, 2) -> 9` |
| `memoryview(object)` | Creates a memoryview object referencing the given object's buffer | `memoryview(b'abc')` |
| `min(iterable, *[, key, default])` | Returns the smallest item in an iterable or among arguments | `min([4, 1, 8]) -> 1` |
| `next(iterator[, default])` | Retrieves the next item from the iterator by calling its `__next__()` method | `next(it)` |
| `object()` | Returns a new featureless object (base for all classes) | `obj = object()` |
| `oct(x)` | Converts an integer to an octal string prefixed with `0o` | `oct(8) -> '0o10'` |
| `open(file, mode='r', ...)` | Opens file and returns a corresponding file object | `open('file.txt', 'r')` |
| `ord(c)` | Returns the Unicode code point integer of a single character | `ord('a') -> 97` |
| `pow(base, exp[, mod])` | Returns base to the power exp; if mod is present, returns base to exp modulo mod | `pow(2, 3) -> 8`, `pow(2, 3, 5) -> 3` |
| `print(*objects, sep=' ', end='\n', file=None, flush=False)` | Prints objects to the text stream file | `print("Hello", "World", sep="-")` |
| `property(fget=None, fset=None, fdel=None, doc=None)` | Returns a property attribute | `@property` |
| `range(stop)` / `range(start, stop[, step])` | Returns an immutable sequence of numbers | `list(range(3)) -> [0, 1, 2]` |
| `repr(object)` | Returns a string containing a printable representation of an object | `repr("hi") -> "'hi'"` |
| `reversed(seq)` | Returns a reverse iterator over the values of sequence | `reversed([1, 2, 3])` |
| `round(number[, ndigits])` | Rounds number to ndigits decimal places (using round half to even) | `round(2.675, 2)` |
| `set([iterable])` | Returns a new set object | `set([1, 2, 2, 3]) -> {1, 2, 3}` |
| `setattr(object, name, value)` | Assigns the value to the attribute of the object | `setattr(user, 'age', 30)` |
| `slice(stop)` / `slice(start, stop[, step])` | Returns a slice object representing set of indices | `s[slice(0, 2)]` |
| `sorted(iterable, *, key=None, reverse=False)` | Returns a new sorted list from the items in iterable | `sorted([3, 1, 2]) -> [1, 2, 3]` |
| `staticmethod(function)` | Transforms a method into a static method | `@staticmethod` |
| `str(object='')` | Returns a string version of object | `str(123) -> '123'` |
| `sum(iterable, /, start=0)` | Sums start and the items of an iterable from left to right | `sum([1, 2, 3]) -> 6` |
| `super()` | Returns a proxy object that delegates method calls to a parent or sibling class | `super().__init__()` |
| `tuple([iterable])` | Creates a new tuple | `tuple([1, 2]) -> (1, 2)` |
| `type(object)` / `type(name, bases, dict)` | Returns the type of an object or creates a new type object | `type(42) -> <class 'int'>` |
| `vars([object])` | Returns the `__dict__` attribute for a module, class, instance, or any other object | `vars(obj)` |
| `zip(*iterables, strict=False)` | Iterates over several iterables in parallel, producing tuples with an item from each | `zip([1, 2], ['a', 'b'])` |
