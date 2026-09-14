# Collections and Itertools Modules

The `collections` and `itertools` modules provide high-performance specialized container datatypes and memory-efficient looping constructs.

---

## 1. The `collections` Module

### Overview of Container Types

| Type | Purpose |
| :--- | :--- |
| `Counter` | Dictionary subclass for counting hashable objects |
| `defaultdict` | Dictionary subclass that calls a factory function to supply missing values |
| `deque` | Double-ended queue with $O(1)$ appends and pops from either end |
| `ChainMap` | Dictionary-like class for creating a single view of multiple mappings |
| `OrderedDict` | Dictionary subclass that remembers insertion order (with re-ordering methods) |

<div class="example-box">
<div class="example-title">Example: Frequency Counting with Counter</div>

```python
from collections import Counter

votes = ["Alice", "Bob", "Alice", "Charlie", "Alice", "Bob"]
tally = Counter(votes)

print("Total Tally:", tally)
print("Winner:", tally.most_common(1)[0][0])
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
Total Tally: Counter({'Alice': 3, 'Bob': 2, 'Charlie': 1})
Winner: Alice
</div>
</div>

---

## 2. The `itertools` Module

The `itertools` module provides fast, memory-efficient building blocks for iterator manipulation.

### Infinite Iterators

- `itertools.count(start=0, step=1)`: Infinite sequence of numbers $0, 1, 2, ...$
- `itertools.cycle(iterable)`: Cycles through an iterable indefinitely.
- `itertools.repeat(elem, n)`: Repeats an element $n$ times.

### Combinatoric Iterators

| Function | Description | Example |
| :--- | :--- | :--- |
| `itertools.product(*iterables)` | Cartesian product | `product('AB', '12') -> A1, A2, B1, B2` |
| `itertools.permutations(p, r)` | Permutations (order matters) | `permutations('ABC', 2) -> AB, AC, BA, BC, CA, CB` |
| `itertools.combinations(p, r)` | Combinations (order does not matter) | `combinations('ABC', 2) -> AB, AC, BC` |

<div class="example-box">
<div class="example-title">Example: Batching and Pairwise Operations</div>

```python
import itertools

# 1. itertools.pairwise (Python 3.10+): Successive overlapping pairs
sequence = [10, 20, 35, 55]
differences = [b - a for a, b in itertools.pairwise(sequence)]
print("Step Differences:", differences)

# 2. itertools.batched (Python 3.12+): Split sequence into chunks of n
data = range(1, 10)
batches = list(itertools.batched(data, n=3))
print("Batches:", batches)
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
Step Differences: [10, 15, 20]
Batches: [(1, 2, 3), (4, 5, 6), (7, 8, 9)]
</div>
</div>
