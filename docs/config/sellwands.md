# Sellwands
 
A sellwand is a vanilla item (stick, blaze rod, nether star, …)
whose right-click on a chest, barrel, or shulker box bulk-sells the
container's contents through `drops.yml` × the wand's multiplier.
Each successful sale debits one use from the wand.
 
Sellwands are defined in `plugins/eGens/sellwands.yml`.
 
## Master switches
 
`config.yml`:
 
```yaml
sellwand:
  enabled: true             # disable to delegate to e.g. DeluxeSellwands
  cooldown-seconds: 5       # min interval between sells per player
```
 
## Preset schema
 
```yaml
config-version: 1
 
wands:
  starter:
    material: STICK
    multiplier: 1.50
    default-uses: 250
    display-name: "<gradient:#9be7a7:#7df9ff><bold><sc:Starter Sellwand></bold></gradient>"
    lore:
      - "<#cfcfcf>Multiplier: <#9be7a7><multiplier>×</#9be7a7>"
      - "<#cfcfcf>Uses: <#9be7a7><uses-left></#9be7a7>"
 
  veteran:
    material: BLAZE_ROD
    multiplier: 2.50
    default-uses: 500
    # … display-name + lore …
 
  master:
    material: NETHER_STAR
    multiplier: 5.00
    default-uses: -1            # unlimited
    # … display-name + lore …
```
 
| Field | Notes |
|---|---|
| `material` | Any 1.21.x material. Determines the wand's visual. |
| `multiplier` | Float ≥ 1.0. Applied to the per-item `price:` value from `drops.yml`. |
| `default-uses` | Positive int = limited-use wand. `-1` = unlimited. When uses hit 0 the item is consumed. |
| `display-name` | MiniMessage. Placeholders below available. |
| `lore` | List of MiniMessage lines. Same placeholders. |
 
### Placeholders in `display-name` / `lore`
 
| Placeholder | Value |
|---|---|
| `<wand-id>` | Preset id (e.g. `starter`). |
| `<multiplier>` | Multiplier formatted to two decimals (`1.50`). |
| `<uses-left>` | Current uses remaining. Renders as the localised "permanent" string for unlimited wands. |
| `<default-uses>` | The preset's `default-uses` value (raw int). |
 
## Issuing a wand
 
```
/egens sellwand list
/egens sellwand give <player> <preset> [uses]
```
 
`uses` defaults to the preset's `default-uses`.
 
## Anti-dupe
 
Each minted wand stamps a unique `wand_uuid` into the item's
persistent data container and the database table that tracks
remaining uses. If a player clones the item (e.g. via a creative
exploit), only the canonical database row is debited — the
duplicate becomes inert after a single sale.
 
Mismatches are recorded in the audit log and the **smaller** value
always wins so cloning never increases the available uses.
 
## Stacking with events
 
The `SELL_MULTIPLIER` event multiplies the *final* payout, so a
2× event with a 1.5× wand gives a `1.5 × 2 = 3.0×` payout. Sellwand
multipliers don't interact with sell-all unlock cost; the unlock
cost is constant and is paid once per scope.
 
## Hot-reload
 
`/egens reload` re-reads `sellwands.yml`. Changes apply to wands
*minted after the reload*. Existing in-world wands keep the
multiplier they were minted with — it's stamped on the row when
the wand is created, so renaming a preset doesn't accidentally
change the value of wands already in players' inventories.
 
---
 
[Tip on Ko-fi](https://ko-fi.com/epromite/tip) keeps maintenance going.
