# Classes and Object-Oriented Programming (OOP)

Python is an object-oriented language. Almost everything in Python is an object with properties and methods. A **class** is a blueprint for creating objects.

---

## 1. Defining a Class

Classes are created using the `class` keyword. The `__init__()` method is the constructor that runs whenever a new instance is instantiated.

### Syntax
```python
class ClassName:
    def __init__(self, parameter1, parameter2):
        self.attribute1 = parameter1
        self.attribute2 = parameter2

    def method_name(self):
        # method statements
```

<div class="example-box">
<div class="example-title">Example: Defining and Instantiating a Class</div>

```python
class Car:
    def __init__(self, brand: str, model: str, year: int):
        self.brand = brand
        self.model = model
        self.year = year
        self.odometer_reading = 0

    def get_description(self) -> str:
        return f"{self.year} {self.brand} {self.model}"

    def update_odometer(self, mileage: int):
        if mileage >= self.odometer_reading:
            self.odometer_reading = mileage
        else:
            print("Cannot roll back an odometer.")

# Create an object instance
my_car = Car("Toyota", "Camry", 2024)
print(my_car.get_description())
my_car.update_odometer(1500)
print("Mileage:", my_car.odometer_reading)
```

<div class="terminal-output-label">Output</div>
<div class="terminal-output">
2024 Toyota Camry
Mileage: 1500
</div>
</div>

---

## Understanding `self`

The `self` parameter is a reference to the current instance of the class. It is used to access variables and methods that belong to that specific instance.

---

## Instance Variables vs Class Variables

- **Instance variables**: Unique to each instance (defined inside `__init__` via `self.x`).
- **Class variables**: Shared among all instances of the class (defined directly under the `class` header).

```python
class Employee:
    # Class variable
    company_name = "Tech Corp"

    def __init__(self, name: str, salary: float):
        # Instance variables
        self.name = name
        self.salary = salary

e1 = Employee("Alice", 75000)
e2 = Employee("Bob", 68000)

print(e1.company_name) # Tech Corp
print(e2.company_name) # Tech Corp
```

---

## 2. Inheritance and `super()`

Inheritance allows a new class (child/subclass) to inherit attributes and methods from an existing class (parent/superclass):

<div class="example-box">
<div class="example-title">Example: Class Inheritance</div>

```python
class ElectricCar(Car):
    def __init__(self, brand: str, model: str, year: int, battery_size: int):
        # Call parent constructor using super()
        super().__init__(brand, model, year)
        self.battery_size = battery_size

    def describe_battery(self):
        print(f"This car has a {self.battery_size}-kWh battery.")

my_tesla = ElectricCar("Tesla", "Model 3", 2025, 75)
print(my_tesla.get_description()) # Inherited method
my_tesla.describe_battery()        # Subclass-specific method
```
</div>

---

## 3. Class Methods and Static Methods

- `@classmethod`: Receives the class (`cls`) as its first argument rather than an instance (`self`). Often used for alternative constructors.
- `@staticmethod`: Does not receive an implicit first argument (`self` or `cls`). Behaves like a regular function scoped within the class.

```python
class Date:
    def __init__(self, year: int, month: int, day: int):
        self.year = year
        self.month = month
        self.day = day

    @classmethod
    def from_string(cls, date_str: str):
        year, month, day = map(int, date_str.split("-"))
        return cls(year, month, day)

    @staticmethod
    def is_valid_month(month: int) -> bool:
        return 1 <= month <= 12

d = Date.from_string("2026-09-14")
print(f"Year: {d.year}, Month: {d.month}, Day: {d.day}")
print("Is Month 9 Valid:", Date.is_valid_month(9))
```

---

## 4. Private Attributes and Name Mangling

Python does not have strict private access specifiers (`private` keyword). Instead:
- `_attribute`: A convention indicating that an attribute is internal/private.
- `__attribute`: Triggers **name mangling**, where Python renames the attribute to `_ClassName__attribute` to prevent accidental overriding in subclasses.

---

## 5. Dataclasses (`@dataclass`)

The `dataclasses` module provides a decorator to automatically generate special methods such as `__init__()`, `__repr__()`, and `__eq__()` on user-defined classes:

<div class="example-box">
<div class="example-title">Example: Using @dataclass</div>

```python
from dataclasses import dataclass

@dataclass
class Book:
    title: str
    author: str
    price: float
    isbn: str

book1 = Book("Fluent Python", "Luciano Ramalho", 49.99, "978-1491957660")
book2 = Book("Fluent Python", "Luciano Ramalho", 49.99, "978-1491957660")

# Auto-generated clean __repr__:
print(book1)

# Auto-generated value-based __eq__:
print(book1 == book2)  # True
```
</div>
