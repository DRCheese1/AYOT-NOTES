<%*
let name = await tp.system.prompt("Name of this building?");
let id = name.toLowerCase().replace(/\s+/g, "-");
let buildingType = (await tp.system.prompt("Building type (e.g. smeltery, sawmill, mineshaft, lumberjack)?")).toLowerCase();
let directHarvest = await tp.system.suggester(["Yes", "No"], [true, false], false, "Does this building harvest a resource directly, with no recipe (e.g. Lumberjack's Hut)?");
let harvestOutput = "";
let harvestTime = "";
if (directHarvest) {
  harvestOutput = `[[${await tp.system.prompt("Resource it directly harvests?")}]]`;
  harvestTime = await tp.system.prompt("Base harvest time (seconds)?", "1");
}
let usesFuel = await tp.system.suggester(["Yes", "No"], [true, false], false, "Does this building require fuel?");
let maxFuelTier = "";
if (usesFuel) {
  maxFuelTier = Number(await tp.system.prompt("Max fuel tier this building can handle?", "1"));
}
-%>
---
id: <% id %>
type: building
building_type: <% buildingType %>
tier: <% Number(await tp.system.prompt("Tier?", "1")) %>
category: <% (await tp.system.prompt("Category (e.g. processing, production, crafting, harvesting)?")).toLowerCase() %>
subcategory: <% (await tp.system.prompt("Subcategory (e.g. refining, extraction, logging)?")).toLowerCase() %>
speed: <% Number(await tp.system.prompt("Crafting/harvest speed multiplier?", "1")) %>
harvest_output: <% harvestOutput %>
harvest_time: <% harvestTime %>
max_fuel_tier: <% maxFuelTier %>
status: <% await tp.system.suggester(["idea", "designing", "final"], ["idea", "designing", "final"], false, "Status?") %>
cost:
---

# <% name %>

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