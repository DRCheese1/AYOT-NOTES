<%*
let name = await tp.system.prompt("Name of this tile?");
let id = name.toLowerCase().replace(/\s+/g, "-");
let harvestersInput = await tp.system.prompt("Harvesters, as type:tier comma-separated (e.g. mineshaft:1, quarry:2)?");
let harvesters = harvestersInput.split(",").map(entry => {
  let [type, tier] = entry.split(":").map(s => s.trim());
  return { type: type.toLowerCase(), min_tier: Number(tier) };
});
let harvestersYaml = harvesters.map(h => `  - type: ${h.type}\n    min_tier: ${h.min_tier}`).join("\n");

let provides = [];
let keepAdding = true;
while (keepAdding) {
  let resource = await tp.system.prompt("Resource this tile provides? (leave blank to stop adding)");
  if (!resource || resource.trim().length === 0) break;
  let time = Number(await tp.system.prompt(`Base harvest time for ${resource} (seconds)?`, "1"));
  provides.push({ resource: resource.trim(), time: time });
}
let providesYaml = provides.length > 0
  ? provides.map(p => `  - resource: "[[${p.resource}]]"\n    time: ${p.time}`).join("\n")
  : "";
-%>
---
id: <% id %>
type: tile
harvested_by:
<% harvestersYaml %>
status: <% await tp.system.suggester(["idea", "designing", "final"], ["idea", "designing", "final"], false, "Status?") %>
provides:
<% providesYaml %>
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

```dataview
TABLE p.resource as "Resource", p.time as "Base Time (s)"
FROM ""
WHERE file.link = this.file.link
FLATTEN provides AS p
```

## Notes