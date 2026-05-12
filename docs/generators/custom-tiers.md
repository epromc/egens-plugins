# Custom tiers

You can ship your own chain alongside (or instead of) the bundled
ones without writing any Java. Drop a new YAML file under
`plugins/eGens/generators/` and run `/egens reload`.

## Minimal tier example

```yaml
# plugins/eGens/generators/my_chain.yml
generators:

  bronze:
    display-name: "<gradient:#cd7f32:#f4a460><bold>Bronze Generator</bold></gradient>"
    lore:
      - "<#cfcfcf><sc:A copper-tin alloy, polished to a sheen.>"
      - "<#7a7a7a><sc:Tier> <#cd7f32><tier>"
      - "<#7a7a7a><sc:Level> <#dcdcdc><level>/<max-level>"
    material: COPPER_BLOCK
    base-capacity: 800
    base-durability: 100
    tick-interval: 2100
    max-level: 10
    next-tier: silver        # optional chain pointer
    repair-cost: 1200
    repair-cost-per-level: 0.6
    shop-price: 4000
    drops:
      - copper_ingot
      - amethyst_shard
      - bronze_coin           # custom drop, defined in drops.yml

  silver:
    display-name: "<gradient:#cccccc:#ffffff><bold>Silver Generator</bold></gradient>"
    material: IRON_BLOCK
    base-capacity: 1200
    base-durability: 130
    tick-interval: 1900
    max-level: 12
    repair-cost: 2400
    repair-cost-per-level: 0.7
    shop-price: 8500
    drops:
      - silver_ingot
      - emerald
```

## Tier fields

| Field | Required | Notes |
|---|---|---|
| `display-name` | yes | MiniMessage. Used in chat, GUI titles, hologram nameplates. |
| `lore` | yes | List of MiniMessage lines. Placeholders below available. |
| `material` | yes | Bukkit material id. The world block placed for this tier. |
| `base-capacity` | yes | Loosely interpreted "yield" knob — feeds the loot-roll output multiplier. |
| `base-durability` | yes | Per-generator durability budget in `DURABILITY` / `HYBRID` corruption modes. |
| `tick-interval` | yes | Milliseconds between ticks. Lower = faster generator. |
| `max-level` | yes | Cap for `/egens upgrade` within this tier. |
| `next-tier` | no | Tier id this tier promotes to via `/egens upgrade` tier promotion. Omit / null for terminal tiers. |
| `repair-cost` | yes | Base cost (currency) for a full repair. |
| `repair-cost-per-level` | yes | Multiplier added to the base cost per generator level. |
| `shop-price` | yes | Price in the generator shop. |
| `drops` | yes | List of drop ids from `generators/drops.yml`. |
| `corrupted-material` | no | Per-tier override for `generator.corrupted-material`. |

### Lore placeholders

| Placeholder | Value |
|---|---|
| `<tier>` | The tier id (e.g. `bronze`). |
| `<level>` | Current generator level (rendered live). |
| `<max-level>` | The tier's `max-level`. |
| `<time>` | Human-readable tick interval (`2.1s`). |
| `<durability>` | Current durability (rendered live). |

## File discovery

- Path: `plugins/eGens/generators/<anything>.yml`.
- Reserved name: `drops.yml` is **not** scanned as a chain.
- All other `.yml` files under `generators/` are loaded in
  alphabetical order.

## Uniqueness

Tier ids must be globally unique across **all** chain files. If
two files declare a `gold` tier, the loader logs an error and
refuses to start until you resolve the conflict.

## Drop references

Every entry in `drops:` must exist in `generators/drops.yml`. The
chain loader logs an explicit warning at boot when a reference is
missing and skips the unknown drop. Add it to `drops.yml`, then
`/egens reload`.

## Validation

After authoring a chain, `/egens reload` parses the file and
either:

- ✓ Loads silently — the tier is now active.
- ✗ Logs a parse error and keeps the previous in-memory state. The
  reload doesn't "half-apply"; you either get the new chain in
  full or fall back to the previous state.

For a deeper sanity check, run `/egens give <you> <tier-id> 1` in
a creative test world and place the block. The first generator
tick happens within `tick-interval` ms.

## Tips

- **Start by copying a bundled chain.** The bundled chains are
  hand-tuned reference points. Start with a copy, rename the
  tiers, and adjust numbers — that's a more reliable path than
  authoring from scratch.
- **Don't reuse drop ids across chains** unless you mean it. The
  registry is global, so two chains pointing at the same drop id
  produce visually identical items. That's often desirable (e.g.
  `iron_ingot` appearing in multiple tiers), but it's worth being
  intentional about.
- **Test corruption visibility.** If you change `material:`, also
  verify what the cracked variant looks like — that's
  `corruption.cracked-material` in `config.yml` (or your tier's
  `corrupted-material:` override).

---

[Tip on Ko-fi](https://ko-fi.com/epromite/tip) keeps maintenance going.
