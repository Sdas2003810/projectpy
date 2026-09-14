# Parallelism & Concurrency (`threading`, `multiprocessing`, `concurrent.futures`)

When your Python programs need to handle multiple tasks simultaneously — such as downloading hundreds of web pages or crunching large scientific matrices across multiple CPU cores — you must choose the right concurrency model:

1. **Threading (`threading`)**: Best for **I/O-bound tasks** (network requests, disk I/O, user input).
2. **Multiprocessing (`multiprocessing`)**: Best for **CPU-bound tasks** (data processing, cryptography, image manipulation).
3. **Asynchronous I/O (`asyncio`)**: Single-threaded cooperative multitasking for massive I/O concurrency.

---

## Understanding the GIL (Global Interpreter Lock)

In standard CPython, the **Global Interpreter Lock (GIL)** is a mutex that prevents multiple native threads from executing Python bytecodes simultaneously.

```
       CPU Core 1                    CPU Core 2
   +-----------------+           +-----------------+
   |  Thread 1 (GIL) |           |  Thread 2 (idle)|
   +--------+--------+           +--------+--------+
            |                             |
            | Releases GIL during I/O     | Acquires GIL
            v                             v
   +-----------------+           +-----------------+
   |  Thread 1 (I/O) |           |  Thread 2 (GIL) |
   +-----------------+           +-----------------+
```

- **I/O-Bound Work**: When a thread waits for network responses or disk reads, it releases the GIL. Other threads can immediately run. Hence, **threading provides real performance speedups for I/O operations**.
- **CPU-Bound Work**: Because threads must take turns holding the GIL, running CPU-heavy calculations in multiple threads actually runs *slower* than a single thread due to context switching overhead. Use **multiprocessing** instead.

!!! note "Python 3.13+ Free-Threading (PEP 703)"
    Starting in Python 3.13, experimental builds of CPython can disable the GIL entirely (`--disable-gil`), allowing true multi-core parallel execution of threads.

---

## The `threading` Module

### Creating and Running Threads

```python
import threading
import time

def fetch_data(source_id: int):
    print(f"[Thread {source_id}] Starting network fetch...")
    time.sleep(1.0)  # Simulating network latency
    print(f"[Thread {source_id}] Completed.")

threads = []
for i in range(4):
    t = threading.Thread(target=fetch_data, args=(i,))
    threads.append(t)
    t.start()  # Launch thread

for t in threads:
    t.join()   # Wait for thread to complete

print("All network requests finished.")
```

### Thread Synchronization: `Lock`

When multiple threads read and modify shared state simultaneously, a **race condition** occurs. Use `threading.Lock` to guarantee atomic access:

```python
import threading

counter = 0
counter_lock = threading.Lock()

def increment():
    global counter
    for _ in range(100_000):
        # Using context manager to acquire and release the lock safely
        with counter_lock:
            counter += 1

threads = [threading.Thread(target=increment) for _ in range(5)]
for t in threads: t.start()
for t in threads: t.join()

print(f"Final counter: {counter}")  # Guaranteed to be 500,000
```

---

## The `multiprocessing` Module

The `multiprocessing` module bypasses the GIL entirely by spawning separate operating system processes, each with its own Python interpreter and private memory space.

```python
import multiprocessing
import math

def calculate_heavy_primes(limit: int) -> int:
    count = 0
    for num in range(2, limit):
        if all(num % i != 0 for i in range(2, int(math.isqrt(num)) + 1)):
            count += 1
    return count

if __name__ == "__main__":
    # IMPORTANT: On Windows and macOS, multiprocessing code MUST be protected
    # by `if __name__ == '__main__':` to prevent recursive child process spawning!
    
    with multiprocessing.Pool(processes=multiprocessing.cpu_count()) as pool:
        limits = [100_000, 200_000, 300_000, 400_000]
        results = pool.map(calculate_heavy_primes, limits)
        print(f"Prime counts: {results}")
```

---

## Modern High-Level Concurrency: `concurrent.futures`

In modern Python, you rarely need to manage raw `Thread` or `Process` instances manually. The `concurrent.futures` module provides a clean, unified abstraction:

- `ThreadPoolExecutor`: Thread pool for I/O
- `ProcessPoolExecutor`: Process pool for CPU

=== "ThreadPoolExecutor (I/O-Bound)"
    ```python
    from concurrent.futures import ThreadPoolExecutor, as_completed
    import urllib.request

    URLS = [
        "https://httpbin.org/delay/1",
        "https://httpbin.org/delay/1",
        "https://httpbin.org/delay/1",
    ]

    def load_url(url: str) -> int:
        with urllib.request.urlopen(url, timeout=5) as response:
            return len(response.read())

    # Concurrently fetch URLs using up to 5 worker threads
    with ThreadPoolExecutor(max_workers=5) as executor:
        future_to_url = {executor.submit(load_url, url): url for url in URLS}
        
        for future in as_completed(future_to_url):
            url = future_to_url[future]
            try:
                data_len = future.result()
                print(f"{url} returned {data_len} bytes")
            except Exception as exc:
                print(f"{url} generated an exception: {exc}")
    ```

=== "ProcessPoolExecutor (CPU-Bound)"
    ```python
    from concurrent.futures import ProcessPoolExecutor
    import math

    def is_prime(n: int) -> bool:
        if n < 2: return False
        for i in range(2, int(math.isqrt(n)) + 1):
            if n % i == 0: return False
        return True

    if __name__ == "__main__":
        numbers = [10_000_019, 10_000_079, 10_000_101, 10_000_103]
        
        with ProcessPoolExecutor() as executor:
            # map preserves input order
            results = list(executor.map(is_prime, numbers))
            print(dict(zip(numbers, results)))
    ```

---

## Which Concurrency Model Should You Choose?

| Scenario | Recommended Approach | Tool |
| :--- | :--- | :--- |
| **Heavy math, image resizing, cryptography** | Multiprocessing | `concurrent.futures.ProcessPoolExecutor` |
| **Downloading files, database queries, disk reads** | Threading | `concurrent.futures.ThreadPoolExecutor` |
| **10,000+ simultaneous web sockets / HTTP calls** | Asynchronous Event Loop | `asyncio` |
| **Simple background cron or timer** | Thread | `threading.Thread(daemon=True)` |
