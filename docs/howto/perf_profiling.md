# Performance Profiling & Optimization HOWTO

Before optimizing any Python program, you must follow Donald Knuth's fundamental rule:
> *"Premature optimization is the root of all evil."*

Never guess where bottlenecks are. Always measure with profilers to identify the exact functions consuming CPU cycles or leaking RAM.

---

## Micro-benchmarking with `timeit`

The `timeit` module accurately measures the execution time of small code snippets by disabling garbage collection and repeating execution thousands of times to eliminate system jitter.

### Command-Line Benchmarking

Compare two approaches directly in your terminal:

```bash
# Benchmark list comprehension
python -m timeit "[x**2 for x in range(1000)]"

# Benchmark map() with lambda
python -m timeit "list(map(lambda x: x**2, range(1000)))"
```

<div class="terminal-output">
10000 loops, best of 5: 38.2 usec per loop
10000 loops, best of 5: 79.5 usec per loop
</div>

The list comprehension is more than twice as fast!

### In-Code Benchmarking

```python
import timeit

setup_code = "data = list(range(10_000))"
test_code = "9999 in data"

# Run 1,000 repetitions
elapsed = timeit.timeit(stmt=test_code, setup=setup_code, number=1000)
print(f"Total time for 1,000 searches: {elapsed:.6f} seconds")
```

---

## Full-Program CPU Profiling with `cProfile`

`cProfile` is a built-in C-extension profiler with minimal overhead. It records every function call, invocation count, and execution duration.

### Profiling from the Command Line

```bash
python -m cProfile -s cumtime my_script.py
```

- `-s cumtime`: Sorts output by cumulative time spent in each function.

Sample output:

<div class="terminal-output">
   Ordered by: cumulative time

   ncalls  tottime  percall  cumtime  percall filename:lineno(function)
        1    0.001    0.001    1.420    1.420 server.py:1(<module>)
        1    0.002    0.002    1.250    1.250 server.py:45(process_records)
     1000    1.120    0.001    1.120    0.001 database.py:12(execute_query)
    50000    0.128    0.000    0.128    0.000 {method 'strip' of 'str' objects}
</div>

### Understanding Profile Columns

| Column | Meaning |
| :--- | :--- |
| `ncalls` | Total number of calls to this function |
| `tottime` | Total time spent in this function **excluding calls to sub-functions** |
| `percall` | `tottime` divided by `ncalls` |
| `cumtime` | Total time spent in this function **including all sub-functions** |
| `filename:lineno`| File, line number, and function name |

---

## Memory Leak Detection with `tracemalloc`

`tracemalloc` tracks every memory block allocated by Python, recording the exact file and line number responsible:

```python
import tracemalloc

# 1. Start tracking allocations
tracemalloc.start()

# 2. Run memory-intensive operations
cache = [f"Record string payload #{i}" * 100 for i in range(100_000)]

# 3. Take snapshot and display top memory consumers
snapshot = tracemalloc.take_snapshot()
top_stats = snapshot.statistics("lineno")

print("[ Top 3 Memory Allocations ]")
for stat in top_stats[:3]:
    print(stat)

# Inspect peak memory usage
current, peak = tracemalloc.get_traced_memory()
print(f"Peak RAM used: {peak / (1024 * 1024):.2f} MB")
tracemalloc.stop()
```

<div class="terminal-output">
[ Top 3 Memory Allocations ]
script.py:7: size=32.4 MiB, count=100000, average=340 B
Peak RAM used: 33.10 MB
</div>

---

## Top 5 High-Impact Optimization Strategies

1. **Use `__slots__` on high-volume classes**: Eliminates instance `__dict__` overhead, saving ~60% RAM when creating millions of objects.
2. **Choose the right data structures**: Set lookups (`item in my_set`) are $O(1)$, while list lookups (`item in my_list`) are $O(N)$.
3. **Use local variables in tight loops**: Resolving local variables in CPython bytecode is faster than resolving global variables or object attributes.
4. **Leverage the Faster CPython improvements**: Python 3.11 introduced the Specializing Adaptive Interpreter, and Python 3.13 added an experimental JIT compiler. Simply upgrading Python versions yields 20–30% speedups for free.
5. **Offload compute to C extensions**: For scientific matrices, use NumPy; for parsing, use `json` or `orjson`.
