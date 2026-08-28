---
type: recipe
building_type: "[[Smeltery]]"
min_tier: 1
input: "[[Iron ore]]"
output: "[[Iron ingot]]"
tier: 1
time: 3
min_fuel_tier: 1
fuel_override: ""
---

# Iron to Iron ingot

Made in any **`= link(this.building_type)`** of Tier `= this.min_tier` or higher. Base craft time: **`= this.time`s**. `= choice(this.min_fuel_tier, "Requires fuel tier " + this.min_fuel_tier + " or higher.", "No fuel required.")`

## Valid Fuel

```dataview
TABLE fuel_tier as "Fuel Tier", burn_time as "Burn Time (s)"
FROM ""
WHERE type = "resource" AND fuel_tier AND (
  (this.fuel_override AND file.link = this.fuel_override) OR
  (!this.fuel_override AND this.min_fuel_tier AND fuel_tier >= this.min_fuel_tier)
)
SORT fuel_tier ASC
```

## Notes