# Asyncio and Concurrency

Asynchronous programming enables writing concurrent programs using an **event loop**, allowing applications to handle thousands of input/output (I/O) bound operations without multi-threading overhead.

In Python, the `asyncio` module provides the framework for writing asynchronous code using the `async` and `await` keywords.

---

## Synchronous vs Asynchronous Execution

- **Synchronous (Blocking)**: Tasks execute sequentially. If a task is waiting for a response from an external network server or database, the entire thread pauses and blocks.
- **Asynchronous (Non-blocking)**: While one task waits for an I/O operation to complete, the event loop switches to execute other pending tasks.

---

## Coroutines: `async def` and `await`

- A function defined with `async def` is a **coroutine function**. Calling it returns a **coroutine object** without executing the body immediately.
- The `await` expression suspends the execution of the coroutine until the awaited result is ready, returning control to the event loop.

<div class="example-box">
<div class="example-title">Example: Basic Coroutine with asyncio.run()</div>

```python
import asyncio

async def fetch_record(record_id: int, delay: float):
    print(f"Starting fetch for record {record_id}...")
    await asyncio.sleep(delay) # Non-blocking sleep
    print(f"Completed fetch for record {record_id}")
    return {"id": record_id, "data": "Sample Payload"}

async def main():
    result = await fetch_record(101, 1.0)
    print("Result:", result)

# Starts the event loop and runs main()
asyncio.run(main())
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
Starting fetch for record 101...
Completed fetch for record 101
Result: {'id': 101, 'data': 'Sample Payload'}
</div>
</div>

---

## Running Tasks Concurrently (`asyncio.TaskGroup`)

In Python 3.11+, `asyncio.TaskGroup` provides an exception-safe context manager for running multiple asynchronous tasks concurrently:

<div class="example-box">
<div class="example-title">Example: Running Multiple Tasks Concurrently</div>

```python
import asyncio
import time

async def download_data(source: str, delay: float):
    print(f"Downloading from {source}...")
    await asyncio.sleep(delay)
    return f"Data from {source}"

async def main():
    start_time = time.perf_counter()

    async with asyncio.TaskGroup() as tg:
        task1 = tg.create_task(download_data("Server A", 1.5))
        task2 = tg.create_task(download_data("Server B", 1.0))
        task3 = tg.create_task(download_data("Server C", 0.5))

    # All tasks complete concurrently
    print(f"Elapsed Time: {time.perf_counter() - start_time:.2f} seconds")
    print("Task 1 Result:", task1.result())

asyncio.run(main())
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
Downloading from Server A...
Downloading from Server B...
Downloading from Server C...
Elapsed Time: 1.51 seconds
Task 1 Result: Data from Server A
</div>
</div>

---

## Concurrency Comparison: Asyncio vs Threading vs Multiprocessing

| Model | Best For | GIL Impact | Resource Overhead |
| :--- | :--- | :--- | :--- |
| **`asyncio`** | High-volume network I/O, Web APIs, WebSockets | Single thread bound | Very low |
| **`threading`** | I/O bound tasks with synchronous libraries | Bound by GIL | Moderate |
| **`multiprocessing`**| CPU-bound computations (data science, encoding) | Bypasses GIL (separate processes) | High (memory per process) |
