# `config.yml`

The main eGens config lives at `plugins/eGens/config.yml`. Every
section is independently hot-reloadable via `/egens reload` unless
flagged otherwise. The bundled file ships with extensive inline
comments — this page is a navigational summary.

## File-level keys

```yaml
config-version: 1   # bumped when a breaking change ships; auto-migrated
```

## `storage`

Pick a backend and configure it. Detail: [Storage](../getting-started/storage.md).

| Key | Default | Notes |
|---|---|---|
| `storage.type` | `SQLITE` | `SQLITE` / `MYSQL` / `MARIADB`. Requires restart. |
| `storage.mysql.host` / `port` / `database` / `username` / `password` | localhost / 3306 / egens / root / *empty* | MySQL connection params. |
| `storage.mysql.pool-size` | `10` | HikariCP pool size. |
| `storage.mysql.use-ssl` | `false` | TLS to the MySQL host. |
| `storage.mysql.properties` | *map* | Free-form JDBC properties forwarded to the driver. |
| `storage.sqlite.file` | `egens.db` | Relative to `plugins/eGens/`. |

## `generator`

Defaults for placed generator blocks.

| Key | Default | Notes |
|---|---|---|
| `generator.default-material` | `IRON_BLOCK` | Fallback block when a tier doesn't override `material:`. |
| `generator.corrupted-material` | `BLACK_WOOL` | World block swap when a generator becomes corrupted. Per-tier override available via `corrupted-material:` inside a tier file. |
| `generator.hologram.enabled` | `true` | Master switch for floating nameplates. |
| `generator.hologram.provider` | `AUTO` | `AUTO`, `NATIVE`, `DECENT`, `FANCY`, `HD`. See [Holograms](../integrations/holograms.md). |
| `generator.hologram.view-distance` | `32` | Block radius. |
| `generator.hologram.show-level` | `true` | Render the `ʟᴇᴠᴇʟ x/y` line. |
| `generator.hologram.show-durability` | `false` | Render the durability line. Off by default. |
| `generator.hologram.text-shadow` | `true` | Vanilla text shadow on each glyph. |

> **Break behaviour.** When a generator is broken it always drops
> the base tier item only — generator state (level, durability,
> stored loot ticks) is never preserved on break. That's intentional.

## `generation`

Controls how generators emit loot per tick.

| Key | Default | Notes |
|---|---|---|
| `generation.drop-scatter` | `0.0` | `0.0` = drop straight up; `1.0` = vanilla scatter velocity. |
| `generation.owner-activity.enabled` | `true` | Skip ticks while the owner is offline or far away. Major performance win for AFK farms. |
| `generation.owner-activity.max-distance-blocks` | `64` | Owner must be within this radius for the generator to tick. |

## `corruption`

When and how generators become "corrupted" (paused until repaired).

| Key | Default | Notes |
|---|---|---|
| `corruption.mode` | `HYBRID` | `CHANCE`, `DURABILITY`, or `HYBRID`. |
| `corruption.chance` | `0.01` | Per-tick corruption chance at level 1. |
| `corruption.chance-at-max-level` | `-1` | `-1` disables level scaling; otherwise chance at max level. |
| `corruption.durability-max` | `100` | Hit-count budget per generator before it auto-corrupts. |
| `corruption.damage-per-tick` | `1` | Durability lost per tick. |
| `corruption.cracked-material` | `COBBLESTONE` | Visual swap when low-durability (DURABILITY mode). |
| `corruption.repair.cooldown-seconds` | `5` | Seconds between repair attempts. |
| `corruption.repair.cost-formula` | `"12.5 * tier"` | Vault-priced. Variables: `tier`, `level`, `durability-missing`. |

## `upgrade`

Cost formulas for `/egens upgrade`.

| Key | Default | Notes |
|---|---|---|
| `upgrade.level.max` | `10` | Hard cap on per-tier level. |
| `upgrade.level.cost-formula` | `"500 * level^1.8"` | Variables: `level` (current), `tier`. |
| `upgrade.promotion.cost-formula` | `"10000 * tier"` | Variables: `level`, `tier` (1-based ordinal of destination tier). |

## `commands`

| Key | Default | Notes |
|---|---|---|
| `commands.target-range` | `6` | Max block-distance for `/egens info`, `upgrade`, `repair`. |

## `events`

Server-wide and personal generator events. Per-event details are in
[`events.yml`](events-yml.md).

| Key | Default | Notes |
|---|---|---|
| `events.enabled` | `true` | Master switch. |
| `events.auto-trigger.enabled` | `true` | Automatically start events from `auto-trigger.pool` every `interval-minutes`. |
| `events.auto-trigger.interval-minutes` | `60` | Spacing between auto-triggered events. |
| `events.auto-trigger.pool` | *list* | Event ids eligible for the auto rotation. |
| `events.stack.max-global` | `1` | Max concurrent server-wide events. `1` = single-event mode. |
| `events.personal.slots.default` | `1` | Per-player slot cap for personal grants. |
| `events.personal.slots.max-cap` | `10` | Upper bound; permission-driven bumps (`egens.personal.slots.<n>`) can't exceed this. |
| `events.bossbar.cycle-interval-millis` | `3000` | Reserved for stacked-bar rotation. |

## `sellwand`

| Key | Default | Notes |
|---|---|---|
| `sellwand.enabled` | `true` | Master switch. Disable to delegate to e.g. DeluxeSellwands. |
| `sellwand.cooldown-seconds` | `5` | Seconds between consecutive sellwand uses. |

Per-preset details are in [`sellwands.yml`](sellwands.md).

## `player-settings.defaults`

Initial values for new players' per-account preferences. They can
override them via [`/egens settings`](../commands/player.md#egens-settings).

| Key | Default | Notes |
|---|---|---|
| `player-settings.defaults.hologram-visible` | `true` | Show the floating nameplate. |
| `player-settings.defaults.sound-effects` | `true` | Generator sound effects. |
| `player-settings.defaults.bossbar` | `true` | Per-event bossbar. |

## `stacking`

| Key | Default | Notes |
|---|---|---|
| `stacking.enabled` | `true` | Allow multiple generators of the same tier to stack on one block. |
| `stacking.max-stack` | `64` | Per-block hard cap. |
| `stacking.multiplier-mode` | `LINEAR` | `LINEAR` (1x per copy) or `DIMINISHING` (less yield per additional copy). |

## `sell-all`

| Key | Default | Notes |
|---|---|---|
| `sell-all.enabled` | `true` | Whether `/egens sellall` is available at all. |
| `sell-all.unlock-scope` | `PER_GENERATOR` | `PER_GENERATOR` or `PER_PLAYER`. |
| `sell-all.unlock-cost.type` | `MONEY` | `MONEY`, `ITEM`, `XP`. |
| `sell-all.unlock-cost.amount` | `5000` | Cost in the selected currency. |

> Auto-sell mode is intentionally **not** supported — designed out
> to prevent dupe edge cases. Don't add it.

## `limits`

| Key | Default | Notes |
|---|---|---|
| `limits.default-max-per-player` | `50` | Base per-player cap across all worlds. |
| `limits.additive-permission-prefix` | `egens.addlimit.` | Suffix is parsed as an integer; **highest** suffix wins. |
| `limits.bypass-permission` | `egens.limit.bypass` | Skip every cap. |
| `limits.per-chunk` | `8` | Max generators per chunk (any owner). `-1` disables. |
| `limits.per-world.<world>` | *(per world)* | Per-world per-owner override. `-1` = uncapped. |

## `backup`

| Key | Default | Notes |
|---|---|---|
| `backup.auto-backup` | `true` | Periodic snapshot timer master switch. |
| `backup.interval-hours` | `6` | Hours between auto snapshots. `0` disables periodic. |
| `backup.retention-count` | `7` | Keep this many most-recent snapshots. `0` disables pruning. |
| `backup.directory` | `backups` | Relative paths resolve under `plugins/eGens/`. |

See [Backups](../operations/backup.md) for the full mechanic.

## `worlds`

| Key | Default | Notes |
|---|---|---|
| `worlds.mode` | `BLACKLIST` | `BLACKLIST` (block listed worlds) or `WHITELIST` (allow only listed). |
| `worlds.list` | `[creative_world]` | World names to apply the rule to. |
| `worlds.bypass-permission` | `egens.world.bypass` | Skip the filter. |
| `worlds.disable-existing` | `false` | Pause existing generators in newly-blacklisted worlds instead of breaking them. |

## `anti-dupe`

| Key | Default | Notes |
|---|---|---|
| `anti-dupe.audit-log` | `true` | Persist every state-changing event in the audit table. |
| `anti-dupe.cancel-explosion` | `true` | Block TNT/creeper damage to generators. |
| `anti-dupe.cancel-piston` | `true` | Block piston relocation of generators. |
| `anti-dupe.cancel-fluid-flow` | `true` | Block water/lava destroying generators. |
| `anti-dupe.flush-interval-ticks` | `200` | Audit-log flush cadence. |
| `anti-dupe.use-row-lock` | `true` | MySQL-only. Required for multi-instance safety. |

## `gui`

| Key | Default | Notes |
|---|---|---|
| `gui.menu-title` | `"<tier-display>"` | MiniMessage; `<tier>` and `<tier-display>` placeholders available. |
| `gui.sounds.enabled` | `true` | Menu click sound effects. |
| `gui.drop-display` | `PERCENT` | `PERCENT` or `WEIGHT` for the drop-list panel. |

Per-menu layouts are in [`gui/*.yml`](gui-yml.md).

## `performance`

| Key | Default | Notes |
|---|---|---|
| `performance.tick-jitter` | `true` | Spread generator ticks across the second to avoid spike alignment. |
| `performance.async-loot-roll` | `true` | Roll loot tables off the main thread. |
| `performance.hologram-update-ticks` | `20` | Refresh interval for nameplates (1 tick = ~50ms). |

See [Performance](../operations/performance.md).

## `profiler`

| Key | Default | Notes |
|---|---|---|
| `profiler.enabled` | `false` | Lightweight tick-time sampler. Off by default. |
| `profiler.top-n` | `10` | Entries shown by `/egens profile`. |

## `integrations`

| Key | Default | Notes |
|---|---|---|
| `integrations.vault` | `AUTO` | `AUTO` / `INTERNAL`. |
| `integrations.placeholderapi` | `AUTO` | `AUTO` / `INTERNAL`. |
| `integrations.shop-provider` | `AUTO` | `AUTO`, `INTERNAL`, `SHOPGUIPLUS`, `ECONOMYSHOPGUI`. |
| `integrations.protection` | `AUTO` | `AUTO`, `NONE`, `WORLDGUARD`, `GRIEFPREVENTION`, `LANDS`, `TOWNY`. |

See the [integrations section](../integrations/vault.md) for adapter-specific notes.

## `update-checker`

| Key | Default | Notes |
|---|---|---|
| `update-checker.enabled` | `true` | Master switch. `false` = no network call ever. |
| `update-checker.source` | `MODRINTH` | Only `MODRINTH` is implemented. |
| `update-checker.slug` | `egens` | Modrinth project slug. |
| `update-checker.notify-permission` | `egens.admin.update` | Who sees the chat notice on join. |
| `update-checker.interval-minutes` | `360` | Recheck cadence after startup. `0` disables. |

## `bstats`

| Key | Default | Notes |
|---|---|---|
| `bstats.enabled` | `true` | Plugin-level switch. Servers can also opt out globally via `plugins/bStats/config.yml`. |
| `bstats.plugin-id` | `-1` | Project ID once registered at https://bstats.org. `-1` skips submission. |

See [Metrics](../operations/metrics.md).

## `locale`

| Key | Default | Notes |
|---|---|---|
| `locale.default` | `id_ID` | Fallback locale id. |
| `locale.available` | `[id_ID, en_US]` | Bundled locales. |

See [Localization](lang.md).

## `debug`

| Key | Default | Notes |
|---|---|---|
| `debug` | `false` | Verbose logging. Toggle in-game with `/egens debug`. |

---

[Tip on Ko-fi](https://ko-fi.com/epromite/tip) keeps maintenance going.
