# Generator overview

A **generator** in eGens is a placed block that ticks on a fixed
interval and produces loot for its owner. Generators belong to a
**tier** (`wood`, `iron`, `diamond`, …); tiers are organised into
**chains** that the player promotes up by spending in-game currency.

This page is a high-level model of how the pieces fit together.
Detailed schemas live under [`/config/`](../config/config-yml.md)
and the [premade tier chains](premade-chains.md) page.

## Concepts

```
Player → places → Generator block
                    ├── tier         (wood, iron, diamond, …)
                    ├── level        (within the tier, 1 → max-level)
                    ├── durability   (corruption budget)
                    ├── owner uuid
                    └── ticks every <tick-interval> ms
                          └── rolls loot from drops.yml entries
                                listed in the tier's `drops:` field
                                → items spawn directly above the block
```

### Tier

A **tier** is one rung in a progression. It's defined in a YAML
file under `generators/`. Each tier has:

- `display-name`, `lore` — visual identity.
- `material` — the world block.
- `base-capacity`, `base-durability`, `tick-interval` — economy
  knobs.
- `max-level` — how many `/egens upgrade` levels are available
  within the tier.
- `next-tier` (optional) — chain pointer for tier promotion.
- `shop-price` — what the shop charges for the placeable item.
- `repair-cost`, `repair-cost-per-level` — economy of repairs.
- `drops:` — list of drop ids from `drops.yml` rolled on each tick.

### Chain

A **chain** is a set of tiers linked by `next-tier`. eGens ships
three chains by default — `vanilla_chain.yml`, `mob_chain.yml`,
`elemental_chain.yml` — but you can:

- Replace any of them.
- Add more chains (every `.yml` file under `generators/` other
  than `drops.yml` is scanned at boot).
- Mix tiers across chains (`vanilla:diamond` can `next-tier:
  elemental:fire` for instance — chains are just naming
  conventions, the runtime treats every tier id as flat).

Tier ids must be globally unique across all chain files. The
loader rejects duplicates with an explicit error at boot.

### Drop registry

`generators/drops.yml` is the single source of truth for every
sellable item. Tier files reference drops **by id**, not by
Material, so the same drop can be reused across tiers without
duplicating its definition. This is also what enables sellable
custom-head drops — the world drop is stamped with the drop id in
its persistent data, and the sell pipeline looks the price back up
in `drops.yml`.

See [Drops registry](drops-registry.md) for the schema.

### Generator state

Every placed generator has its own row in the database tracking:

- Tier id, level, durability, owner uuid.
- Last-tick timestamp (for tick-interval enforcement).
- Optional cosmetic state (hologram visibility, sound preference).

Generator state is **never** preserved on break. Breaking a
generator always drops the base tier item only — level,
durability, and any in-flight ticks are lost. This is intentional;
it removes a whole class of dupe vectors.

## Loot generation pipeline

1. **Tick scheduler** wakes a generator every `tick-interval` ms
   (per-region thread on Folia, per-world thread on Paper).
2. **Owner activity gate** — if the owner is offline or further
   than `owner-activity.max-distance-blocks` away, the tick is
   skipped.
3. **Per-generator lock** is acquired so concurrent threads can't
   double-tick the same block. This is the anti-dupe boundary.
4. **Loot roll** uses the tier's `drops:` list, weighted by each
   drop's `weight:`. The roll happens off-thread when
   `performance.async-loot-roll: true`.
5. **Corruption check** rolls a per-tick corruption chance and
   decrements durability. If the generator corrupts, the world
   block is swapped to `corruption.cracked-material` and ticks are
   paused until repaired.
6. **Items spawn** directly above the block. `generation.drop-scatter`
   controls the initial velocity (0.0 = straight up, 1.0 = vanilla
   scatter).
7. **Audit log** records the tick (if `anti-dupe.audit-log: true`).

## Upgrade pipeline

Two upgrade kinds are available via `/egens upgrade`:

- **Level upgrade** — within the current tier, up to `max-level`.
  Cost: `upgrade.level.cost-formula` evaluated with the
  generator's current `level`.
- **Tier promotion** — when the generator is at `max-level` and
  the tier has a `next-tier:` pointer. Cost:
  `upgrade.promotion.cost-formula` evaluated with the destination
  tier's 1-based ordinal.

Quotes are returned without charging. The player runs the command
a second time to confirm. If state changes between the quote and
confirmation (e.g. a concurrent upgrade by an admin), the second
call refunds and aborts with `command.upgrade.desync`.

## Corruption + repair

- `corruption.mode: CHANCE` — pure random chance per tick.
- `corruption.mode: DURABILITY` — every tick costs one durability
  point; when it hits zero the generator corrupts.
- `corruption.mode: HYBRID` (default) — both checks active.

A corrupted generator paused via `cracked-material` swap. The
owner runs `/egens repair` (or the Repair tile in the generator
GUI) to restore it, paying the formula in
`corruption.repair.cost-formula`.

## Limits

Placement caps are evaluated in order, lowest precedence first:

1. **Per-world** (`limits.per-world.<world>`) — overrides per-player
   for listed worlds.
2. **Per-chunk** (`limits.per-chunk`) — hard cap per chunk across
   all owners.
3. **Per-player** (`limits.default-max-per-player` + the additive
   permission prefix).

`egens.limit.bypass` skips them all.

## Where to next?

- [Premade tier chains](premade-chains.md) — what ships in the
  bundled `generators/` folder.
- [Custom tiers](custom-tiers.md) — author your own chain in YAML.
- [Drops registry](drops-registry.md) — the `drops.yml` schema.
- [Balance guide](balance-guide.md) — tweak tick interval, capacity,
  price.

---

[Tip on Ko-fi](https://ko-fi.com/epromite/tip) keeps maintenance going.
