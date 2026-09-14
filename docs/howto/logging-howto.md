# Logging HOWTO

Every production application needs observability. While `print()` statements help with quick interactive debugging, they are unsuitable for production: they lack timestamps, cannot be filtered by severity, lack destination routing (console vs file vs cloud collector), and fail to capture execution tracebacks cleanly.

The standard library `logging` module provides a flexible event-logging framework for applications and libraries.

---

## Log Severity Levels

Python defines five standard severity levels (plus `NOTSET`):

| Level | Numeric Value | When to Use |
| :--- | :--- | :--- |
| `DEBUG` | 10 | Detailed diagnostic information for developers |
| `INFO` | 20 | Confirmation that things are working as expected |
| `WARNING` | 30 | An indication that something unexpected happened or an issue may occur soon (Default level) |
| `ERROR` | 40 | A serious problem: the application was unable to perform some function |
| `CRITICAL` | 50 | A catastrophic failure: program may be unable to continue running |

---

## Basic Configuration with `basicConfig()`

For simple scripts, configure the root logger using `logging.basicConfig()`:

```python
import logging

logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s [%(levelname)s] %(name)s: %(message)s",
    datefmt="%Y-%m-%d %H:%M:%S"
)

logging.info("Application service starting up...")
logging.warning("Database response latency is above threshold (450ms)")
logging.error("Failed to authenticate user: connection reset")
```

<div class="terminal-output">
2026-09-14 14:10:00 [INFO] root: Application service starting up...
2026-09-14 14:10:00 [WARNING] root: Database response latency is above threshold (450ms)
2026-09-14 14:10:00 [ERROR] root: Failed to authenticate user: connection reset
</div>

---

## The Four Core Components of Logging

Enterprise applications configure the four modular components:

```
[ Application Code ]
        |
        v
    [ Logger ]  (e.g., logging.getLogger(__name__))
        |
        +---- Pass records to Handlers
        |
    [ Handler ] (e.g., StreamHandler, RotatingFileHandler)
        |
        +---- Filter records with [ Filter ]
        |
        +---- Format text with   [ Formatter ]
        |
        v
[ Destination ] (Console, File, ElasticSearch)
```

---

## Professional Pattern: Named Loggers & Handlers

Always create a module-scoped logger using `__name__`:

```python
import logging
from logging.handlers import RotatingFileHandler

# 1. Create named logger
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)  # Capture everything at logger level

# 2. Console Handler (outputs WARNING and higher to stdout)
console_handler = logging.StreamHandler()
console_handler.setLevel(logging.WARNING)
console_format = logging.Formatter("[%(levelname)s] %(message)s")
console_handler.setFormatter(console_format)

# 3. Rotating File Handler (rotates when log reaches 10MB, retains 5 backups)
file_handler = RotatingFileHandler(
    "app.log",
    maxBytes=10 * 1024 * 1024,  # 10 MB
    backupCount=5,
    encoding="utf-8"
)
file_handler.setLevel(logging.DEBUG)
file_format = logging.Formatter(
    "%(asctime)s - %(name)s - %(levelname)s - %(funcName)s:%(lineno)d - %(message)s"
)
file_handler.setFormatter(file_format)

# 4. Attach handlers to logger
logger.addHandler(console_handler)
logger.addHandler(file_handler)
```

---

## Logging Exceptions with Full Tracebacks

When catching exceptions, use `logger.exception()` (or pass `exc_info=True`) to automatically attach the complete stack traceback to the log record:

```python
def divide(a: float, b: float) -> float:
    try:
        return a / b
    except ZeroDivisionError:
        logger.exception("Mathematical error encountered in division operation")
        return 0.0

divide(10, 0)
```

<div class="terminal-output">
[ERROR] Mathematical error encountered in division operation
Traceback (most recent call last):
  File "calc.py", line 3, in divide
    return a / b
ZeroDivisionError: division by zero
</div>

---

## Structured JSON Logging for Cloud Environments

In containerized deployments (Kubernetes, AWS CloudWatch, Datadog), logs should be formatted as single-line JSON objects:

```python
import json
import logging
from datetime import datetime, timezone

class JsonFormatter(logging.Formatter):
    def format(self, record):
        log_record = {
            "timestamp": datetime.now(timezone.utc).isoformat(),
            "level": record.levelname,
            "logger": record.name,
            "message": record.getMessage(),
            "module": record.module,
            "line": record.lineno
        }
        if record.exc_info:
            log_record["exception"] = self.formatException(record.exc_info)
        return json.dumps(log_record)

handler = logging.StreamHandler()
handler.setFormatter(JsonFormatter())
cloud_logger = logging.getLogger("api_gateway")
cloud_logger.addHandler(handler)
cloud_logger.setLevel(logging.INFO)

cloud_logger.info("Order placed successfully")
```

<div class="terminal-output">
{"timestamp": "2026-09-14T08:40:00+00:00", "level": "INFO", "logger": "api_gateway", "message": "Order placed successfully", "module": "orders", "line": 42}
</div>
