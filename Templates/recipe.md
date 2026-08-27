<%*
let name = await tp.system.prompt("Name of this recipe?");
let buildingType = (await tp.system.prompt("Building type this recipe needs (e.g. smeltery)?")).toLowerCase();
let requiresFuel = await tp.system.suggester(["Yes", "No"], [true, false], false, "Does this recipe require fuel?");
let minFuelTier = "";
let fuelOverride = "";
if (requiresFuel) {
  minFuelTier = Number(await tp.system.prompt("Minimum fuel tier required?", "1"));
  let overrideInput = await tp.system.prompt("Specific fuel override? (leave blank to allow any valid-tier fuel)", "");
  if (overrideInput.trim().length > 0) {
    fuelOverride = `[[${overrideInput.trim()}]]`;
  }
}
-%>
---
type: recipe
building_type: <% buildingType %>
min_tier: <% Number(await tp.system.prompt("Minimum building tier required?", "1")) %>
input: "[[<% await tp.system.prompt("Input resource name?") %>]]"
output: "[[<% await tp.system.prompt("Output resource name?") %>]]"
tier: <% Number(await tp.system.prompt("Recipe tier?", "1")) %>
time: <% Number(await tp.system.prompt("Base craft time (seconds)?", "1")) %>
min_fuel_tier: <% minFuelTier %>
fuel_override: "<% fuelOverride %>"
---

# <% name %>

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