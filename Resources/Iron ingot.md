---
id: iron-ingot
tier: 1
category: metal
status: designing
fuel_tier:
burn_time:
---

# Iron ingot

This is a Tier `= this.tier` `= this.category` resource, currently **`= this.status`**.

## Description

An iron ingot is a refined version of [[Iron ore]] that has been smelted with a [[Smeltery]]. Used in many recipies.
## Ways to Obtain

### Crafted (Recipes)

```dataview
TABLE building_type as "Building Type", input as "From", time as "Base Time (s)"
FROM ""
WHERE type = "recipe" AND output = this.file.link
```

### Harvested Directly (No Recipe)

```dataview
TABLE building_type as "Building Type", tier as "Tier", harvest_time as "Base Time (s)"
FROM ""
WHERE type = "building" AND harvest_output = this.file.link
```

### Found On Tiles

```dataview
TABLE h.type as "Harvest With", h.min_tier as "Min Tier"
FROM ""
WHERE type = "tile" AND contains(provides, this.file.link)
FLATTEN harvested_by AS h
```

## Used In

```dataview
TABLE building_type as "Building Type", output as "Produces", time as "Base Time (s)"
FROM ""
WHERE type = "recipe" AND input = this.file.link
```

## Used As Fuel In

```dataview
TABLE building_type as "Building Type", min_fuel_tier as "Min Fuel Tier Required"
FROM ""
WHERE type = "recipe" AND this.fuel_tier AND (
  (fuel_override AND fuel_override = this.file.link) OR
  (!fuel_override AND min_fuel_tier AND min_fuel_tier <= this.fuel_tier)
)
```

## Notes