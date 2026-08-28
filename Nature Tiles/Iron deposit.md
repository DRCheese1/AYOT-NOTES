---
id: iron-deposit
type: tile
harvested_by:
  - type: mineshaft
    min_tier: 1
  - type: quarry
    min_tier: 1
status: designing
provides: "[[Iron ore]]"
---

# Iron deposit

## Description

A deposit of [[Iron ore]] that you can harvest with a [[Mineshaft]].
## Buildings That Can Harvest This

```dataview
TABLE tier as "Tier", speed as "Speed"
FROM ""
WHERE type = "building" AND length(filter(this.harvested_by, (h) => h.type = building_type AND tier >= h.min_tier)) > 0
SORT tier ASC
```

## Resources Provided


## Notes