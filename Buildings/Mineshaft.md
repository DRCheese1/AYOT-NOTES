---
id: mineshaft
type: building
building_type: mineshaft
tier: 1
category: excavation
subcategory: ore
speed: 1
harvest_output:
  - - Iron ore
harvest_time: 3
max_fuel_tier:
status: designing
cost:
---

# Mineshaft

This is a Tier `= this.tier` `= link(this.building_type)` (`= this.category`, `= this.subcategory`), with a **`= this.speed`x** speed multiplier, currently **`= this.status`**.

## Description


## Placement


## Recipes (Crafted)

```dataview
TABLE input as "Input", output as "Output", time as "Base Time (s)", round(60 * this.speed / time, 2) as "Per Min"
FROM ""
WHERE type = "recipe" AND building_type = this.building_type AND min_tier <= this.tier
SORT min_tier ASC
```

## Direct Harvest

```dataview
TABLE harvest_output as "Harvests", harvest_time as "Base Time (s)", round(60 * speed / harvest_time, 2) as "Per Min"
FROM ""
WHERE file.link = this.file.link AND harvest_output
```

## Compatible Fuel

```dataview
TABLE fuel_tier as "Fuel Tier", burn_time as "Burn Time (s)"
FROM ""
WHERE type = "resource" AND fuel_tier AND fuel_tier <= this.max_fuel_tier
SORT fuel_tier DESC
```

## Tiles This Can Harvest

```dataview
LIST
FROM ""
WHERE type = "tile"
FLATTEN harvested_by AS h
WHERE h.type = this.building_type AND h.min_tier <= this.tier
```

## Notes