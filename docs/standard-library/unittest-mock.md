# Mocking and Testing: `unittest.mock`

The `unittest.mock` module provides a core library for mocking objects in Python unit tests. It allows you to replace parts of your system under test with mock objects and make assertions about how they have been used.

Mocking is essential for testing components in isolation without triggering real network requests, executing database transactions, reading disk files, or relying on non-deterministic system state (such as the system clock or random numbers).

---

## `Mock` vs `MagicMock`

The `Mock` class creates flexible objects that accept any method call or attribute access without raising an `AttributeError`. 

`MagicMock` is a subclass of `Mock` that additionally implements standard Python "magic" (dunder) methods (such as `__len__`, `__iter__`, `__getitem__`, `__enter__`, `__exit__`, and `__str__`). In almost all test scenarios, **use `MagicMock`**.

```python
from unittest.mock import MagicMock

# Create a mock database repository
mock_repo = MagicMock()

# Any attribute or method exists automatically
mock_repo.find_user_by_id.return_value = {"id": 1, "username": "alice"}

user = mock_repo.find_user_by_id(1)
print(user)  # {'id': 1, 'username': 'alice'}

# Magic methods work out of the box
mock_repo.__len__.return_value = 42
print(len(mock_repo))  # 42
```

---

## Configuring Mocks: `return_value` and `side_effect`

### 1. `return_value`
Use `return_value` when the mock should always return a static result:

```python
from unittest.mock import Mock

mock_api = Mock()
mock_api.get_temperature.return_value = 21.5

print(mock_api.get_temperature())  # 21.5
print(mock_api.get_temperature())  # 21.5
```

### 2. `side_effect`
`side_effect` allows dynamic behavior depending on the input:

#### Raising an Exception
```python
mock_client = Mock()
mock_client.connect.side_effect = ConnectionError("Database unreachable")

try:
    mock_client.connect()
except ConnectionError as e:
    print("Caught expected error:", e)
```

#### Returning Sequential Values
Pass an iterable to return a different value on each consecutive invocation:

```python
mock_generator = Mock()
mock_generator.get_next_id.side_effect = [101, 102, 103]

print(mock_generator.get_next_id())  # 101
print(mock_generator.get_next_id())  # 102
print(mock_generator.get_next_id())  # 103
```

#### Dynamic Callable Behavior
Pass a function to dynamically calculate the return value based on the arguments:

```python
mock_calc = Mock()
mock_calc.multiply.side_effect = lambda a, b: a * b

print(mock_calc.multiply(6, 7))  # 42
```

---

## The `@patch` Decorator

The `patch()` decorator is used to temporarily replace an object or function at a target import location with a mock for the duration of a test.

### Golden Rule: Patch Where an Object is LOOKED UP
Always patch the name in the module **where it is imported and used**, not in the module where it is declared.

Suppose you have:
```python
# File: my_service.py
import requests

def fetch_exchange_rate(currency: str) -> float:
    response = requests.get(f"https://api.example.com/rates/{currency}")
    return response.json()["rate"]
```

To test `fetch_exchange_rate` without hitting the internet:

```python
# File: test_service.py
import unittest
from unittest.mock import patch, MagicMock
from my_service import fetch_exchange_rate

class TestExchangeRate(unittest.TestCase):
    # Patch 'requests.get' inside 'my_service', NOT 'requests.get' directly
    @patch("my_service.requests.get")
    def test_successful_fetch(self, mock_get):
        # 1. Configure the simulated HTTP response
        mock_response = MagicMock()
        mock_response.status_code = 200
        mock_response.json.return_value = {"rate": 1.08}
        mock_get.return_value = mock_response

        # 2. Call the function under test
        rate = fetch_exchange_rate("EUR")

        # 3. Assertions on the result
        self.assertEqual(rate, 1.08)

        # 4. Assertions on the mock interactions
        mock_get.assert_called_once_with("https://api.example.com/rates/EUR")

if __name__ == "__main__":
    unittest.main()
```

---

## Verifying Mock Interactions

`unittest.mock` records all calls made to mock objects. You can assert that methods were called with the expected arguments:

| Assertion Method | Description |
| :--- | :--- |
| `mock.assert_called()` | Assert the mock was called at least once. |
| `mock.assert_called_once()` | Assert the mock was called exactly once. |
| `mock.assert_called_with(*args, **kwargs)` | Assert the *most recent* call was made with specific arguments. |
| `mock.assert_called_once_with(*args, **kwargs)` | Assert the mock was called exactly once, and with the specified arguments. |
| `mock.assert_not_called()` | Assert the mock was never called. |
| `mock.call_count` | Number of times the mock was invoked. |
| `mock.call_args` | Arguments of the most recent call. |
| `mock.call_args_list` | List of all calls made to the mock. |

```python
from unittest.mock import Mock

notifier = Mock()

notifier.send_email("user@test.com", subject="Welcome")

# Verification
notifier.send_email.assert_called_once()
notifier.send_email.assert_called_once_with("user@test.com", subject="Welcome")
print("Total calls:", notifier.send_email.call_count)  # 1
```

---

## Patching as a Context Manager

When you only need a mock active for a few specific lines of code, use `patch` as a context manager:

```python
from unittest.mock import patch

def generate_filename() -> str:
    import time
    return f"export_{int(time.time())}.csv"

# Temporarily freeze time for the test
with patch("time.time", return_value=1700000000.0):
    filename = generate_filename()
    assert filename == "export_1700000000.csv"

# Outside the block, time.time() returns the real current time again
```

---

## Asynchronous Mocking: `AsyncMock`

For testing asynchronous code (`async def`), standard library Python provides `AsyncMock`:

```python
import asyncio
from unittest.mock import AsyncMock, patch

async def fetch_user_data(user_id: int):
    # Simulated async network call
    await asyncio.sleep(1)
    return {"id": user_id, "status": "active"}

async def main_test():
    with patch("__main__.fetch_user_data", new_callable=AsyncMock) as mock_fetch:
        mock_fetch.return_value = {"id": 42, "status": "mocked"}
        
        result = await mock_fetch(42)
        
        assert result == {"id": 42, "status": "mocked"}
        mock_fetch.assert_awaited_once_with(42)
        print("Async test passed successfully!")

asyncio.run(main_test())
```

---

## Summary Best Practices

1. **Patch Where Used**: Always target the module where the function is imported and called (`app.module.target_func`), not where it was authored.
2. **Use `spec=True`**: Pass `spec=TargetClass` or `autospec=True` to prevent tests from passing when calling methods that don't actually exist on the real class.
3. **Keep Tests Independent**: Use `mock.reset_mock()` or place `@patch` decorators on individual test methods to ensure clean state between test runs.
