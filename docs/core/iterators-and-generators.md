# Iterators and Generators

Iteration is fundamental to Python — `for` loops, comprehensions, `map()`, `zip()`, `sorted()`, and dozens of standard library functions all use it. Understanding how the iterator protocol works lets you write memory-efficient code and make your own objects iterable.

---

## The Iterator Protocol

Python's iteration model is built on two concepts:

- An **iterable** is any object you can loop over. It has an `__iter__()` method that returns an iterator.
- An **iterator** is the object that does the actual stepping. It has a `__next__()` method that returns the next value, and raises `StopIteration` when exhausted.

```python
# Every for loop is really doing this:
my_list = [1, 2, 3]
iterator = iter(my_list)      # calls my_list.__iter__()

print(next(iterator))  # 1 — calls iterator.__next__()
print(next(iterator))  # 2
print(next(iterator))  # 3
next(iterator)         # StopIteration!
```

Most iterables (lists, tuples, strings, dicts) are *not* their own iterators — calling `iter()` on them returns a separate iterator object. But iterators are their own iterators (calling `iter()` on an iterator returns `self`).

---

## Making Your Own Iterable Class

```python
class CountUp:
    """Iterates from start to stop (exclusive)."""

    def __init__(self, start: int, stop: int):
        self.start = start
        self.stop = stop

    def __iter__(self):
        return CountUpIterator(self.start, self.stop)


class CountUpIterator:
    def __init__(self, current: int, stop: int):
        self.current = current
        self.stop = stop

    def __iter__(self):
        return self   # Iterators must return self

    def __next__(self):
        if self.current >= self.stop:
            raise StopIteration
        value = self.current
        self.current += 1
        return value


for n in CountUp(1, 5):
    print(n)   # 1 2 3 4
```

---

## Generator Functions

Writing separate iterator classes is verbose. **Generator functions** give you the same power with a fraction of the code. A generator function uses `yield` instead of `return`:

```python
def count_up(start, stop):
    current = start
    while current < stop:
        yield current       # pauses here, returns value to caller
        current += 1        # resumes from here on next call

for n in count_up(1, 5):
    print(n)    # 1 2 3 4
```

When you call a generator function, Python doesn't execute any of its body — it returns a **generator object**. The body runs lazily, resuming each time `next()` is called on the generator.

```python
gen = count_up(1, 5)
print(type(gen))        # <class 'generator'>
print(next(gen))        # 1
print(next(gen))        # 2
list(gen)               # [3, 4] — consume the rest
```

---

## Generator Expressions

Like list comprehensions but lazy — values are computed one at a time:

```python
# List comprehension — computes ALL values immediately, stores in memory
squares_list = [x**2 for x in range(1_000_000)]   # uses ~8MB of RAM

# Generator expression — computes one at a time
squares_gen = (x**2 for x in range(1_000_000))    # uses ~200 bytes

# Use the same way
for sq in squares_gen:
    if sq > 100:
        break    # stops generating — never computed the rest
```

When you only need to iterate once and don't need all values at once, generators are always more memory-efficient.

---

## `yield from` — Delegating to Sub-Generators

`yield from` delegates to another iterable/generator:

```python
def flatten(nested):
    for sublist in nested:
        yield from sublist   # equivalent to: for item in sublist: yield item

list(flatten([[1, 2], [3, 4], [5, 6]]))  # [1, 2, 3, 4, 5, 6]

# Also works for any iterable
def chain_strings(*args):
    for s in args:
        yield from s

list(chain_strings("ABC", "DEF"))  # ['A', 'B', 'C', 'D', 'E', 'F']
```

---

## Infinite Generators

Generators don't have to end. This is safe because they're lazy:

```python
def naturals(start=0):
    n = start
    while True:
        yield n
        n += 1

# Take the first 5 natural numbers
from itertools import islice
first_five = list(islice(naturals(), 5))   # [0, 1, 2, 3, 4]

# First 5 even numbers
evens = (n for n in naturals() if n % 2 == 0)
print([next(evens) for _ in range(5)])     # [0, 2, 4, 6, 8]
```

---

## Sending Values into Generators

Generators can receive values via `.send()` — making them two-way communication channels (useful for coroutines):

```python
def accumulator():
    total = 0
    while True:
        value = yield total   # yield sends total out; received value comes back
        if value is None:
            break
        total += value

gen = accumulator()
next(gen)           # prime the generator (must call next first)
gen.send(10)        # 10
gen.send(20)        # 30
gen.send(5)         # 35
```

---

## Built-in Iteration Tools

Python's standard toolkit for iteration:

```python
# enumerate — index + value
for i, fruit in enumerate(["apple", "banana", "cherry"]):
    print(i, fruit)          # 0 apple / 1 banana / 2 cherry

enumerate(["a", "b"], start=1)  # start index at 1

# zip — parallel iteration
names = ["Alice", "Bob", "Carol"]
scores = [95, 87, 91]
for name, score in zip(names, scores):
    print(f"{name}: {score}")

# zip with strict=True (Python 3.10+) — raises if lengths differ
list(zip([1, 2], [3, 4, 5], strict=True))   # ValueError!

# zip_longest — fills shorter iterables with fillvalue
from itertools import zip_longest
list(zip_longest([1, 2], [3, 4, 5], fillvalue=0))  # [(1,3),(2,4),(0,5)]

# map — apply function to each element (lazy)
doubled = map(lambda x: x * 2, [1, 2, 3])
list(doubled)   # [2, 4, 6]

# filter — keep elements where function returns True (lazy)
evens = filter(lambda x: x % 2 == 0, range(10))
list(evens)   # [0, 2, 4, 6, 8]

# reversed — iterate in reverse
list(reversed([1, 2, 3]))   # [3, 2, 1]

# sorted — returns a sorted list (works on any iterable)
sorted({3, 1, 4, 1, 5, 9, 2, 6})   # [1, 1, 2, 3, 4, 5, 6, 9] (set has no order)
```

---

## `itertools` — The Iterator Toolbox

```python
import itertools

# chain — iterate multiple iterables as one
list(itertools.chain([1, 2], [3, 4], [5]))   # [1, 2, 3, 4, 5]

# islice — slice a lazy iterator
list(itertools.islice(range(1000), 5))       # [0, 1, 2, 3, 4]
list(itertools.islice(range(1000), 2, 8, 2)) # [2, 4, 6]

# cycle — repeat indefinitely
colors = itertools.cycle(["red", "green", "blue"])
[next(colors) for _ in range(7)]  # ['red', 'green', 'blue', 'red', 'green', 'blue', 'red']

# repeat — repeat a value n times
list(itertools.repeat(0, 5))    # [0, 0, 0, 0, 0]

# count — infinite counter
counter = itertools.count(10, 2)   # start=10, step=2
[next(counter) for _ in range(5)]  # [10, 12, 14, 16, 18]

# combinations and permutations
list(itertools.combinations("ABC", 2))    # [('A','B'),('A','C'),('B','C')]
list(itertools.permutations("ABC", 2))    # [('A','B'),('A','C'),('B','A'),...]

# product — cartesian product
list(itertools.product([0, 1], repeat=3)) # all 3-bit binary combinations

# groupby — group consecutive elements
data = [("a", 1), ("a", 2), ("b", 3), ("b", 4), ("a", 5)]
for key, group in itertools.groupby(data, key=lambda x: x[0]):
    print(key, list(group))
# a [('a', 1), ('a', 2)]
# b [('b', 3), ('b', 4)]
# a [('a', 5)]   ← only groups CONSECUTIVE elements!

# batched (Python 3.12+) — split into fixed-size chunks
list(itertools.batched(range(10), 3))   # [(0,1,2), (3,4,5), (6,7,8), (9,)]
```

See the [Collections and Itertools](../standard-library/collections-itertools.md) page for even more.

---

## Generator-Based Pipelines

Generators compose beautifully into data pipelines — no intermediate lists:

```python
def read_lines(filename):
    with open(filename) as f:
        yield from f

def strip_lines(lines):
    for line in lines:
        yield line.strip()

def filter_empty(lines):
    for line in lines:
        if line:
            yield line

def parse_numbers(lines):
    for line in lines:
        yield float(line)

# Compose the pipeline — nothing runs until we consume
pipeline = parse_numbers(filter_empty(strip_lines(read_lines("data.txt"))))
total = sum(pipeline)   # now it runs — one line at a time, no full file in RAM
```
