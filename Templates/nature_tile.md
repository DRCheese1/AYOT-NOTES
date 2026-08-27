<%*
let name = await tp.system.prompt("Name of this tile?");
let id = name.toLowerCase().replace(/\s+/g, "-");
let harvestersInput = await tp.system.prompt("Harvesters, as type:tier comma-separated (e.g. mineshaft:1, quarry:2)?");
let harvesters = harvestersInput.split(",").map(entry => {
  let [type, tier] = entry.split(":").map(s => s.trim());
  return { type: type.toLowerCase(), min_tier: Number(tier) };
});
let harvestersYaml = harvesters.map(h => `  - type: ${h.type}\n    min_tier: ${h.min_tier}`).join("\n");
-%>
---
id: <% id %>
type: tile
harvested_by:
<% harvestersYaml %>
status: <% await tp.system.suggester(["idea", "designing", "final"], ["idea", "designing", "final"], false, "Status?") %>
provides:
---

# <% name %>

## Description


## Buildings That Can Harvest This

```dataview
TABLE tier as "Tier", speed as "Speed"
FROM ""
WHERE type = "building" AND length(filter(this.harvested_by, (h) => h.type = building_type AND tier >= h.min_tier)) > 0
SORT tier ASC
```

## Resources Provided


## Notes