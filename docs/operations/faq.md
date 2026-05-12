# FAQ

## Does eGens work on Spigot / CraftBukkit?

No. eGens uses Paper-only APIs and a Paper-style scheduler
abstraction so the same jar runs on Paper and Folia. Spigot will
refuse to load it.

## Does eGens support Folia?

Yes — `folia-supported: true` is set in the plugin manifest. Every
scheduler call goes through the eGens scheduler wrapper which uses
Folia's `RegionScheduler` / `GlobalRegionScheduler` when available
and Bukkit's `BukkitScheduler` on classic Paper.

## What Minecraft versions are supported?

1.21 – 1.21.4. The plugin manifest declares `api-version: '1.21'`.
Older versions are not supported and won't load on a 1.21+ server.

## Can I use eGens without Vault?

The plugin loads, places, ticks, and breaks generators without
Vault. But every money-touching feature (upgrade, repair, shop,
sell, sellwand) gates behind Vault — without it, those features
gracefully fall back to "economy missing" messages. See
[Vault](../integrations/vault.md).

## How do I share state across multiple servers?

Switch to MySQL. Point every game server at the same database,
keep `anti-dupe.use-row-lock: true`, and make sure each server's
`plugins/eGens/` folder has the same YAML configs. See
[Storage](../getting-started/storage.md).

## Will pickup-generator (saving state on break) ever be a feature?

No. Pickup-generator was rejected during design as a class of
dupe risk. Breaking a generator always drops the base tier item
only — state is never preserved. Use `/egens give` to hand out
generators to players if you need to migrate them somewhere.

## Will auto-sell ever be a feature?

No. Auto-sell was rejected for the same dupe-risk reason. The
trade-off is one click per sale (or one keybind to `/sell all`).

## How do I add new tiers without losing existing players' progress?

Drop a new chain file in `generators/` (or extend an existing one)
and `/egens reload`. Existing generators keep their tier id, level,
durability, and owner. New tiers become buyable in the shop the
moment the reload completes.

If you *remove* a tier id, generators of that tier become
"unknown" — they stay placed but fail to tick. Re-add the
definition (or migrate the affected rows in the DB) to restore
them.

## How do I migrate from another generator plugin?

There's no built-in migrator from other generator plugins. The
practical recipe:

1. Stand up eGens on a test world with your custom tier chain.
2. Write a one-off SQL script that maps your old plugin's
   generator rows to the eGens schema.
3. Test on a staging snapshot first.

The eGens schema is documented per migration. If you're attempting
a migration from a specific plugin, ask on the Discord — there's
usually someone who's done it.

## Can I run multiple economies (e.g. "tokens" + "gold")?

Today eGens uses a single Vault currency for every charge. The
currency is whichever one Vault exposes as primary. There's no
per-feature currency selector. Multi-currency support is on the
backlog but not in 1.0.

## What happens when a player is banned / leaves the server?

Their generators stay placed but stop ticking (owner-activity
gate). They survive server restarts and are still owned by the
banned player's UUID. To clean them up:

- A staff member with `egens.admin.break` can break them.
- A SQL script can null-out the owner_uuid column (the generators
  remain orphaned and inert until someone with
  `egens.admin.bypass` cleans them up).

## Is there an API for other plugins to integrate?

There is a small public API surface in the `egens-api` module
(events fired on generator place / break / corrupt / repair /
tick). The full Javadoc lives in the plugin source repository.
This documentation site only covers the user-facing surface.

## Where do I get the plugin?

Modrinth: <https://modrinth.com/plugin/egens>. The Modrinth page
is the authoritative source for binary downloads — don't trust
third-party mirrors.

## How do I support development?

Tip on Ko-fi: <https://ko-fi.com/epromite/tip>. Even a one-off
tip pays for hours of debugging and feature work. Thank you.

---

[Tip on Ko-fi](https://ko-fi.com/epromite/tip) keeps maintenance going.
