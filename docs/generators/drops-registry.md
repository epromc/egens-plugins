# Drops registry — `drops.yml`

`plugins/eGens/generators/drops.yml` is the single source of truth
for every sellable item. Tier files (`generators/*_chain.yml`)
reference drops **by id**, not by Material, so the same drop can be
reused across tiers without duplicating its definition.

## Why keyed by id?

Keying drops by id rather than Material is what enables sellable
custom-head drops. Every world drop is stamped with an
`egens:drop_id` persistent-data tag and the sell pipeline reads
that id back to look the price up here. As a side effect:

- Two visually identical items with different `drop_id` stamps can
  have different prices.
- A drop with `item: PLAYER_HEAD` and a custom `head-texture` is a
  fully tracked, sellable, weighted drop.
- Renaming a drop id requires updating every chain file that
  references it (and the loader will warn you).

## Schema

```yaml
drops:
  iron_ingot:
    item: IRON_INGOT
    weight: 80
    min: 1
    max: 2
    price: 5

  diamond:
    item: DIAMOND
    weight: 20
    min: 1
    max: 1
    price: 50
    display-name: "<gradient:#7df9ff:#aef9ff><bold>Diamond</bold></gradient>"
    lore:
      - "<#7df9ff>Pristine carbon lattice."
    enchanted: true
    craft: false

  magma_orb:
    item: PLAYER_HEAD
    head-texture: "ewogICJ0aW1lc3RhbXAi…"
    display-name: "<gradient:#ff8a3d:#ffd166><bold>Magma Orb</bold></gradient>"
    lore:
      - "<#ff8a3d>Pulsing with infernal heat."
    weight: 5
    min: 1
    max: 1
    price: 250
    craft: false
```

| Field | Required | Notes |
|---|---|---|
| `item` | yes | Vanilla Bukkit material constant (case insensitive). |
| `weight` | yes | Roll weight (relative; must be `> 0`). |
| `min` | yes | Inclusive minimum stack size when this drop rolls. |
| `max` | yes | Inclusive maximum stack size when this drop rolls. |
| `price` | yes | Per-unit sell value (Vault currency units). |
| `head-texture` | no | base64 profile-textures payload (only honoured when `item: PLAYER_HEAD`). |
| `display-name` | no | MiniMessage. Overrides the vanilla display name. |
| `lore` | no | List of MiniMessage lore lines. |
| `enchanted` | no | `true` forces the enchanted-glint shimmer. |
| `craft` | no | When `false`, the drop is silently blocked from crafting, anvil, and smithing surfaces — it can only be sold. Default `true`. |

## Adding a new drop

1. Pick a unique id (lowercase + underscores by convention).
2. Add the entry under `drops:` in `drops.yml`.
3. Reference the id from one or more chain files' `drops:` list.
4. Run `/egens reload`.
5. Test with `/egens give <you> <tier> 1` and watch a generator
   roll the drop.

If you skip step 3, the drop is technically registered but never
rolled by any generator. That's fine for "reserved" entries.

## Weights → percentage

Inside one tier's `drops:` list, weights are normalised to a 0–100%
chance per item. The GUI defaults to rendering these as percent
(`gui.drop-display: PERCENT`). For raw weight numbers, flip to
`gui.drop-display: WEIGHT` in `config.yml`.

Example: a tier with `drops: [iron_ingot, diamond]` and the file
above produces `IRON_INGOT` 80% of the time and `DIAMOND` 20% of
the time. Stack size is rolled independently using `min`/`max`.

## Custom-head drops

`item: PLAYER_HEAD` + `head-texture: <base64>` makes a fully
sellable custom head. The base64 payload is the same value used by
plugins like HeadDatabase — paste it verbatim. The drop survives
inventory shuffles, anvil renames, and item-frame display because
the `drop_id` is stamped into the item's persistent-data and
preserved across vanilla operations.

## Craft block

Some drops are designed to flow *only* through the sell pipeline.
Setting `craft: false` is a defensive switch:

- Crafting recipes that consume the item silently fail.
- Anvil + smithing UIs treat the item as a no-op.
- Cooking and brewing reject the item.

Use this for cosmetic / lore drops (orbs, tokens, glyphs) that
exist purely as an "uncraftable currency" the player has to sell.

## Hot reload

`/egens reload` re-parses `drops.yml`. Existing in-world drops keep
their `drop_id` stamp — their price updates the moment the new
config takes effect because price lookup happens at sell time,
not drop time.

---

[Tip on Ko-fi](https://ko-fi.com/epromite/tip) keeps maintenance going.
