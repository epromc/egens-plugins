# Install

eGens is distributed as a single shaded jar. Drop it into your
server's `plugins/` folder and restart.

## Server requirements

| Requirement | Minimum |
|---|---|
| **Server software** | Paper or Folia |
| **Minecraft version** | 1.21 — 1.21.4 |
| **Java runtime** | Java 21 (eGens is compiled against the 1.21 Paper API) |
| **RAM headroom** | A few hundred MB on top of your usual Paper baseline. Loaded chunks with active generators cost more than empty chunks. |
| **Database** | Optional MySQL/MariaDB. SQLite is bundled and used automatically when MySQL credentials aren't configured. See [Storage](storage.md). |

Spigot is **not** supported — eGens uses Paper-only APIs and a
PaperMC-style scheduler abstraction so the same jar runs on
classic Paper and on Folia without changes.

## Optional companion plugins

Drop them in alongside eGens and they will auto-detect. Nothing is
*required* — if a companion plugin is missing, eGens degrades to a
sensible fallback.

| Plugin | What it adds |
|---|---|
| [Vault](https://www.spigotmc.org/resources/vault.34315/) | Currency for upgrades, shop, repairs, sell, sellwand. Without it those features still run in `INTERNAL` mode against a placeholder economy — set `integrations.shop-provider: INTERNAL` if you don't plan to install Vault. |
| [PlaceholderAPI](https://www.spigotmc.org/resources/placeholderapi.6245/) | Drop conditions and event triggers can refer to other plugins' placeholders. |
| [DecentHolograms](https://modrinth.com/plugin/decentholograms) / [FancyHolograms](https://modrinth.com/plugin/fancyholograms) / [HolographicDisplays](https://dev.bukkit.org/projects/holographic-displays) | Floating hologram labels above each generator. eGens falls back to native Minecraft display entities when none of these are installed. |
| [WorldGuard](https://dev.bukkit.org/projects/worldguard) / [GriefPrevention](https://www.spigotmc.org/resources/griefprevention.1884/) / [Lands](https://www.spigotmc.org/resources/lands.53313/) / [Towny](https://www.spigotmc.org/resources/towny-advanced.72694/) | Region protection — eGens asks them whether a player can build at the placement location. |
| [HeadDatabase](https://www.spigotmc.org/resources/headdatabase.14280/) | Optional source of custom head textures for tier icons. |
| [ItemsAdder](https://www.spigotmc.org/resources/itemsadder.73355/) / [Oraxen](https://www.spigotmc.org/resources/oraxen.72448/) | Custom-model item materials in drops are passed through unchanged. |
| [ShopGUIPlus](https://www.spigotmc.org/resources/shopgui.6515/) / [EconomyShopGUI](https://www.spigotmc.org/resources/economyshopgui.69927/) | Alternate shop providers — set `integrations.shop-provider: SHOPGUIPLUS` or `ECONOMYSHOPGUI` in `config.yml`. |
| [DeluxeSellwands](https://www.spigotmc.org/resources/deluxesellwands.91827/) | Coexists peacefully — eGens' built-in sellwand can be disabled via `sellwand.enabled: false`. |
| Multiverse-Core / MyWorlds | World tab-completion in the world whitelist/blacklist. |

## First boot

1. Stop the server.
2. Drop `egens-<version>.jar` into `plugins/`.
3. Start the server.
4. eGens creates `plugins/eGens/` and writes the default
   configuration:
   - `config.yml` — top-level switches.
   - `events.yml` — generator events.
   - `sellwands.yml` — sellwand presets.
   - `generators/vanilla_chain.yml`, `mob_chain.yml`,
     `elemental_chain.yml` — three premade chains (see
     [Premade tier chains](../generators/premade-chains.md)).
   - `generators/drops.yml` — drop registry.
   - `gui/*.yml` — inventory layouts.
   - `lang/id_ID.yml`, `lang/en_US.yml` — bundled locales.
5. Tail the console — `Enabled eGens vX.Y.Z` confirms a clean start.
6. Drop in optional companion plugins now if you want them detected
   on the next reload.

## Stress-test it on a single-player console

Before exposing eGens to a busy server, give yourself OP on a test
world and run:

```
/egens give <you> wood 1
```

Place the wood generator in front of you. Within `tick-interval`
milliseconds (~2.4 s for the bundled `wood` tier) the generator
should start dropping `oak_log`, `stick`, and `oak_sapling`. If
nothing drops, check the console — `place.error` or
`place.world-blocked` messages will explain why.

## Upgrading from an older version

eGens uses YAML `config-version: N` integers per file and a
versioned database schema. On boot:

- Older config files are auto-migrated and a `.bak` is written next
  to the original.
- Older DB schemas run the relevant migrations in order. The
  `egens_schema_version` row tracks the current version — never
  edit it by hand.
- Newer-than-bundled config files load best-effort with a warning.
  Don't downgrade jars unless you also restore an older DB snapshot;
  see [Backups](../operations/backup.md).

## Where to next?

- [Permissions](permissions.md) — who can do what.
- [Storage](storage.md) — switching to MySQL.
- [Commands](../commands/index.md) — the full command surface.

---

[Tip on Ko-fi](https://ko-fi.com/epromite/tip) keeps maintenance going.
