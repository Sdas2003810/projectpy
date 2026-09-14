# Date and Time (`datetime` and `zoneinfo`)

Python supplies the `datetime` and `zoneinfo` modules to represent, manipulate, format, and calculate timestamps across calendars and timezones.

---

## Core Classes in the `datetime` Module

- `date`: Represents year, month, and day (e.g. `2026-09-14`).
- `time`: Represents time of day independent of date (e.g. `13:30:00`).
- `datetime`: Combines both date and time into a single object.
- `timedelta`: Represents a duration or difference between two dates/times.
- `timezone`: Represents a fixed offset from UTC.

---

## 1. Creating and Getting Current Time

<div class="example-box">
<div class="example-title">Example: Current Date and UTC Time</div>

```python
from datetime import datetime, date, timezone

# Today's date
today = date.today()
print("Today's Date:", today)

# Current UTC datetime (Recommended standard for servers and databases)
utc_now = datetime.now(timezone.utc)
print("Current UTC:", utc_now)
```
</div>

---

## 2. Formatting Dates (`strftime`)

Use `.strftime(format)` to format a `datetime` object into a readable string:

### Common Format Directives

| Directive | Meaning | Example |
| :--- | :--- | :--- |
| `%Y` | Year with century as a decimal number | `2026` |
| `%m` | Month as a zero-padded decimal number | `09` |
| `%B` | Full month name | `September` |
| `%d` | Day of the month as zero-padded decimal | `14` |
| `%A` | Full weekday name | `Monday` |
| `%H` | Hour (24-hour clock) `00` to `23` | `13` |
| `%I` | Hour (12-hour clock) `01` to `12` | `01` |
| `%p` | AM or PM | `PM` |
| `%M` | Minute as zero-padded decimal `00` to `59` | `45` |
| `%S` | Second as zero-padded decimal `00` to `59` | `20` |

<div class="example-box">
<div class="example-title">Example: Formatting Datetimes</div>

```python
from datetime import datetime

dt = datetime(2026, 9, 14, 15, 30, 0)
formatted = dt.strftime("%A, %B %d, %Y at %I:%M %p")
print(formatted)
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
Monday, September 14, 2026 at 03:30 PM
</div>
</div>

---

## 3. Parsing Strings into Dates (`strptime`)

Use `datetime.strptime(date_string, format)` to parse a string into a `datetime` object:

<div class="example-box">
<div class="example-title">Example: Parsing Strings</div>

```python
from datetime import datetime

date_str = "2026-10-31 18:45"
parsed_dt = datetime.strptime(date_str, "%Y-%m-%d %H:%M")

print("Parsed Year:", parsed_dt.year)
print("Parsed Month:", parsed_dt.month)
print("Parsed Day:", parsed_dt.day)
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
Parsed Year: 2026
Parsed Month: 10
Parsed Day: 31
</div>
</div>

---

## 4. Date Arithmetic with `timedelta`

Calculate past or future dates by adding or subtracting `timedelta`:

<div class="example-box">
<div class="example-title">Example: Calculating Date Differences</div>

```python
from datetime import datetime, timedelta

current_time = datetime(2026, 9, 14, 12, 0, 0)

# Add 7 days and 3 hours
future_time = current_time + timedelta(days=7, hours=3)
print("Future:", future_time)

# Difference between two dates
difference = future_time - current_time
print("Days Difference:", difference.days)
print("Total Seconds:", difference.total_seconds())
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
Future: 2026-09-21 15:00:00
Days Difference: 7
Total Seconds: 615600.0
</div>
</div>

---

## 5. Working with Timezones (`zoneinfo`)

Python 3.9+ includes the `zoneinfo` module, which uses the system's IANA time zone database:

<div class="example-box">
<div class="example-title">Example: Converting Across Timezones</div>

```python
from datetime import datetime, timezone
from zoneinfo import ZoneInfo

# Create UTC time
utc_time = datetime.now(timezone.utc)

# Convert to New York and Tokyo local times
ny_time = utc_time.astimezone(ZoneInfo("America/New_York"))
tokyo_time = utc_time.astimezone(ZoneInfo("Asia/Tokyo"))

print("New York:", ny_time.strftime("%Y-%m-%d %H:%M:%S %Z"))
print("Tokyo:   ", tokyo_time.strftime("%Y-%m-%d %H:%M:%S %Z"))
```
</div>
