<%*
let name = await tp.system.prompt("Name of this building?");
let id = name.toLowerCase().replace(/\s+/g, "-");
let buildingType = (await tp.system.prompt("Building type (e.g. smeltery, sawmill, mineshaft, lumberjack)?")).toLowerCase();
let usesFuel = await tp.system.suggester(["Yes", "No"], [true, false], false, "Does this building require fuel?");
let maxFuelTier = "";
if (usesFuel) {
  maxFuelTier = Number(await tp.system.prompt("Max fuel tier this building can handle?", "1"));
}
let cost = [];
let keepAdding = true;
while (keepAdding) {
  let item = await tp.system.prompt("Cost item name? (leave blank to stop adding)");
  if (!item || item.trim().length === 0) break;
  let amount = Number(await tp.system.prompt(`Amount of ${item}?`, "1"));
  cost.push({ item: item.trim(), amount: amount });
}
let costYaml = cost.length > 0
  ? cost.map(c => `  - item: "[[${c.item}]]"\n    amount: ${c.amount}`).join("\n")
  : "";
-%>
---
id: <% id %>
type: building
building_type: <% buildingType %>
tier: <% Number(await tp.system.prompt("Tier?", "1")) %>
category: <% (await tp.system.prompt("Category (e.g. processing, production, crafting, excavation)?")).toLowerCase() %>
subcategory: <% (await tp.system.prompt("Subcategory (e.g. refining, extraction, logging)?")).toLowerCase() %>
speed: <% Number(await tp.system.prompt("Crafting/harvest speed multiplier?", "1")) %>
max_fuel_tier: <% maxFuelTier %>
status: <% await tp.system.suggester(["idea", "designing", "final"], ["idea", "designing", "final"], false, "Status?") %>
cost:
<% costYaml %>
---

# <% name %>

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