# Priority Queues & Binary Search: `heapq` and `bisect`

Python provides two specialized, high-performance algorithm modules in its standard library:
- **`heapq`**: Implements binary min-heaps for priority queues and finding the smallest or largest $N$ elements in $O(N \log K)$ time.
- **`bisect`**: Implements binary search algorithms for finding insertion points and maintaining sorted order in lists in $O(\log N)$ time.

---

## The `heapq` Module

A **heap** is a specialized binary tree where the parent node is always less than or equal to its children (a min-heap). In Python, heaps are represented as regular Python `list` objects where:
- `heap[0]` is always the smallest element in the collection.
- Children of `heap[k]` are at `heap[2*k + 1]` and `heap[2*k + 2]`.

### Core Functions

| Function | Complexity | Description |
| :--- | :--- | :--- |
| `heapq.heappush(heap, item)` | $O(\log N)$ | Pushes `item` onto the heap while maintaining the heap invariant. |
| `heapq.heappop(heap)` | $O(\log N)$ | Pops and returns the smallest item (`heap[0]`), maintaining the heap invariant. |
| `heapq.heapify(list)` | $O(N)$ | Transforms an arbitrary list into a valid min-heap in-place. |
| `heapq.heappushpop(heap, item)` | $O(\log N)$ | Pushes `item` and then pops and returns the smallest element (faster than separate calls). |
| `heapq.heapreplace(heap, item)` | $O(\log N)$ | Pops the smallest element first, then pushes `item`. |
| `heapq.nlargest(n, iterable, [key])` | $O(N \log K)$ | Returns a list with the $N$ largest elements from iterable. |
| `heapq.nsmallest(n, iterable, [key])` | $O(N \log K)$ | Returns a list with the $N$ smallest elements from iterable. |

---

### Basic Heap Operations

```python
import heapq

# 1. Start with an empty list and push elements
pq = []
heapq.heappush(pq, 45)
heapq.heappush(pq, 12)
heapq.heappush(pq, 89)
heapq.heappush(pq, 23)

print("Heap internal array:", pq)
# Output: [12, 23, 89, 45] (note: pq[0] is guaranteed smallest)

# 2. Extract elements in ascending order
while pq:
    smallest = heapq.heappop(pq)
    print(smallest, end=" ")
# Output: 12 23 45 89
```

### In-Place `heapify()`
If you already have a list, use `heapq.heapify()` to convert it into a heap in linear time $O(N)$, which is faster than calling `heappush()` $N$ times:

```python
numbers = [64, 34, 25, 12, 22, 11, 90]
heapq.heapify(numbers)

print("Heapified array:", numbers)
print("Smallest element:", numbers[0])  # Always 11
```

---

### Building a Robust Priority Queue

When items have identical priorities, comparing elements directly can fail if the payload object does not implement comparison operators (`<`). To prevent this, include an auto-incrementing task counter in each heap entry:

```python
from dataclasses import dataclass, field
import heapq

@dataclass(order=True)
class PrioritizedTask:
    priority: int
    task_id: int = field(compare=True)
    description: str = field(compare=False)

class PriorityQueue:
    def __init__(self):
        self._heap = []
        self._counter = 0

    def push(self, task_description: str, priority: int) -> None:
        # Lower priority number = higher urgency
        task = PrioritizedTask(priority, self._counter, task_description)
        heapq.heappush(self._heap, task)
        self._counter += 1

    def pop(self) -> str:
        if not self._heap:
            raise IndexError("pop from empty priority queue")
        return heapq.heappop(self._heap).description

    def is_empty(self) -> bool:
        return len(self._heap) == 0

# Usage
queue = PriorityQueue()
queue.push("Write weekly report", priority=3)
queue.push("Fix critical production bug", priority=1)
queue.push("Review pull request", priority=2)

while not queue.is_empty():
    print("Executing:", queue.pop())
```

**Output:**
```text
Executing: Fix critical production bug
Executing: Review pull request
Executing: Write weekly report
```

---

### Finding Top $N$ Elements (`nlargest` & `nsmallest`)

When $N$ is relatively small compared to the collection length, `heapq.nlargest` and `heapq.nsmallest` are significantly faster and more memory-efficient than sorting the entire list:

```python
scores = [
    {"name": "Alice", "score": 92},
    {"name": "Bob", "score": 87},
    {"name": "Charlie", "score": 99},
    {"name": "David", "score": 78},
    {"name": "Eve", "score": 95},
]

# Top 2 scores
top_2 = heapq.nlargest(2, scores, key=lambda x: x["score"])
print("Top 2 performers:", top_2)
# Output: [{'name': 'Charlie', 'score': 99}, {'name': 'Eve', 'score': 95}]
```

---

## The `bisect` Module

The `bisect` module implements binary search algorithms over sorted Python sequences. It locates insertion points where a new element can be inserted while maintaining sorted order.

### Core Functions

| Function | Description |
| :--- | :--- |
| `bisect.bisect_left(a, x, lo=0, hi=len(a), *, key=None)` | Locate the leftmost insertion point for `x` in sorted list `a`. |
| `bisect.bisect_right(a, x, lo=0, hi=len(a), *, key=None)` | Locate the rightmost insertion point for `x` (equivalent to `bisect.bisect()`). |
| `bisect.insort_left(a, x, lo=0, hi=len(a), *, key=None)` | Insert `x` into sorted list `a` at the leftmost valid position. |
| `bisect.insort_right(a, x, lo=0, hi=len(a), *, key=None)` | Insert `x` into sorted list `a` at the rightmost valid position (equivalent to `bisect.insort()`). |

---

### `bisect_left` vs `bisect_right`

The difference between `bisect_left` and `bisect_right` appears when the search value is already present in the list:

```python
import bisect

data = [10, 20, 30, 30, 30, 40, 50]

# Locate insertion points for 30
idx_left = bisect.bisect_left(data, 30)
idx_right = bisect.bisect_right(data, 30)

print(f"bisect_left index: {idx_left}")    # Index 2 (before existing 30s)
print(f"bisect_right index: {idx_right}")  # Index 5 (after existing 30s)
```

---

### Numeric Range & Grade Lookups

A classic and highly optimized use case for `bisect` is mapping continuous numeric ranges to discrete categories without long chains of `if/elif` statements:

```python
import bisect

def grade_lookup(score: int) -> str:
    # Breakpoints represent cutoff thresholds:
    # score < 60 -> F, 60-69 -> D, 70-79 -> C, 80-89 -> B, >= 90 -> A
    cutoffs = [60, 70, 80, 90]
    letter_grades = ['F', 'D', 'C', 'B', 'A']
    
    # bisect_right returns index: 0 for < 60, 1 for 60..69, ..., 4 for >= 90
    index = bisect.bisect_right(cutoffs, score)
    return letter_grades[index]

scores = [55, 60, 75, 89, 90, 100]
print({s: grade_lookup(s) for s in scores})
```

**Output:**
```text
{55: 'F', 60: 'D', 75: 'C', 89: 'B', 90: 'A', 100: 'A'}
```

---

### Custom Key Functions (Python 3.10+)

Starting in Python 3.10, all `bisect` functions accept a `key` parameter, allowing binary search directly on sorted collections of objects:

```python
from dataclasses import dataclass
import bisect

@dataclass
class User:
    username: str
    points: int

users = [
    User("alice", 150),
    User("bob", 280),
    User("charlie", 420),
    User("david", 600),
]

# Find where a user with 350 points belongs
target_points = 350
insert_idx = bisect.bisect_right(users, target_points, key=lambda u: u.points)

print(f"Insert at position {insert_idx}")
# Output: Insert at position 2 (between bob and charlie)
```

---

## When to Use Which?

| Scenario | Recommended Tool | Complexity |
| :--- | :--- | :--- |
| Priority Queue (frequent push and pop of min/max element) | `heapq` (`heappush`, `heappop`) | $O(\log N)$ push/pop |
| Finding top 5 or 10 elements in a large dataset | `heapq.nlargest` / `heapq.nsmallest` | $O(N \log K)$ |
| Lookup in a pre-sorted static list | `bisect.bisect_left` | $O(\log N)$ search |
| Range / bucket / cutoff classification | `bisect.bisect_right` | $O(\log N)$ search |
| Complete sorting of an unsorted list | Built-in `list.sort()` / `sorted()` | $O(N \log N)$ (Timsort) |
