---
id: smeltery
type: building
building_type: smeltery
tier: 1
category: processing
subcategory: refining
speed: 1
max_fuel_tier: 1
status: designing
cost:
  - item: "[[stone]]"
    amount: 30
  - item: "[[log]]"
    amount: 10
---

# Smeltery

This is a Tier `= this.tier` `= this.building_type` (`= this.category`, `= this.subcategory`), with a **`= this.speed`x** speed multiplier, currently **`= this.status`**.

## Description


## Placement


## Cost

```dataview
TABLE c.item as "Resource", c.amount as "Amount"
FROM ""
WHERE file.link = this.file.link
FLATTEN cost AS c
```

## Recipes (Crafted)

```dataview
TABLE input as "Input", output as "Output", time as "Base Time (s)", round(60 * this.speed / time, 2) as "Per Min"
FROM ""
WHERE type = "recipe" AND building_type = this.building_type AND min_tier <= this.tier
SORT min_tier ASC
```

## Direct Harvest (from Tiles)

```dataview
TABLE p.resource as "Resource", p.time as "Base Time (s)", round(60 * this.speed / p.time, 2) as "Per Min"
FROM ""
WHERE type = "tile"
FLATTEN harvested_by AS h
FLATTEN provides AS p
WHERE h.type = this.building_type AND h.min_tier <= this.tier
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