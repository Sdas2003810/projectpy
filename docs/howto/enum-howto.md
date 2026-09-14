# Advanced Enumerations HOWTO

While basic enums map a name to a scalar value, Python's `enum` module supports multi-attribute members, complex behavior calculations, and dynamic enumeration generation.

---

## Multi-Value Enums with Custom `__init__`

Enum members can represent rich entities with multiple properties by defining a custom `__init__()` method:

```python
from enum import Enum

class Planet(Enum):
    # Members defined with tuples: (mass_kg, radius_meters)
    MERCURY = (3.303e+23, 2.4397e+6)
    VENUS   = (4.869e+24, 6.0518e+6)
    EARTH   = (5.976e+24, 6.37814e+6)
    MARS    = (6.421e+23, 3.3972e+6)

    def __init__(self, mass: float, radius: float):
        self.mass = mass
        self.radius = radius

    @property
    def surface_gravity(self) -> float:
        # Universal gravitational constant G = 6.67300E-11
        G = 6.67300e-11
        return G * self.mass / (self.radius ** 2)

    def surface_weight(self, other_mass: float) -> float:
        return other_mass * self.surface_gravity

# Calculate weight of a 75kg astronaut on Mars
astronaut_mass = 75.0
mars_weight = Planet.MARS.surface_weight(astronaut_mass)
earth_weight = Planet.EARTH.surface_weight(astronaut_mass)

print(f"Weight on Earth: {earth_weight:.1f} N")
print(f"Weight on Mars:  {mars_weight:.1f} N")
```

<div class="terminal-output">
Weight on Earth: 735.1 N
Weight on Mars:  278.4 N
</div>

---

## Functional API for Dynamic Enums

When member names are determined at runtime (e.g. read from an external database or config file), construct enums dynamically:

```python
from enum import Enum

# Passing space-separated names (auto-assigned integers starting from 1)
Role = Enum("Role", "ADMIN EDITOR VIEWER GUEST")

print(Role.ADMIN)        # Role.ADMIN
print(Role.ADMIN.value)  # 1

# Or passing dictionary with explicit values:
Status = Enum("Status", {"ACTIVE": "act", "INACTIVE": "inact"})
```

---

## JSON Serialization for Enums

Standard `json.dumps()` raises `TypeError` when attempting to serialize an Enum member. Use this clean encoder recipe:

```python
import json
from enum import Enum

class Color(Enum):
    RED = "#ff0000"
    GREEN = "#00ff00"
    BLUE = "#0000ff"

class EnumEncoder(json.JSONEncoder):
    def default(self, obj):
        if isinstance(obj, Enum):
            return obj.value
        return super().default(obj)

data = {"theme": "dark", "accent_color": Color.GREEN}
json_output = json.dumps(data, cls=EnumEncoder)
print(json_output)
# {"theme": "dark", "accent_color": "#00ff00"}
```
