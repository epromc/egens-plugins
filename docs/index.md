# eGens Documentation

> **eGens** is a next-gen Gens / Tycoon plugin for Paper and Folia
> servers running Minecraft 1.21.x. Players place generator blocks
> that periodically drop loot; admins curate the experience through
> tiered upgrade chains, an event system, shops, sellwands, and
> integrations with the Vault / PlaceholderAPI / hologram /
> protection ecosystem.

This site is the operator-facing manual. Every page describes only
what server admins need to configure or run; internal plugin
architecture lives in the plugin source repository.

---

## Getting started

| Page | What it covers |
|---|---|
| [Install](getting-started/install.md) | Server prerequisites, dropping the jar, first boot, default world layout. |
| [Permissions](getting-started/permissions.md) | Every permission node the plugin registers, with LuckPerms-style examples. |
| [Storage](getting-started/storage.md) | MySQL vs SQLite trade-offs, connection strings, migration on first boot. |

## Commands

| Page | What it covers |
|---|---|
| [Command index](commands/index.md) | One-glance cheat sheet of every `/egens` subcommand and the `/sell` shortcut. |
| [Admin commands](commands/admin.md) | `give`, `reload`, `backup`, `profile`, `debug`, `event`, `sellwand`, ... |
| [Player commands](commands/player.md) | `menu`, `shop`, `info`, `upgrade`, `repair`, `sell`, `sellall`, `settings`, `help`. |

## Configuration

| Page | What it covers |
|---|---|
| [`config.yml`](config/config-yml.md) | Top-level keys: storage, generator defaults, generation, corruption, upgrade, events, sellwand, stacking, sell-all, limits, backup, worlds, anti-dupe, GUI, performance, profiler, integrations, update-checker, bStats, locale, debug. |
| [`events.yml`](config/events-yml.md) | Server-wide and personal events, auto-trigger schedule, stacking, bossbar styling. |
| [`gui/*.yml`](config/gui-yml.md) | Inventory layouts for the main hub, shop, settings, generator menus. |
| [`sellwands.yml`](config/sellwands.md) | Sellwand presets — multiplier, use count, item appearance. |
| [Localization (`lang/*.yml`)](config/lang.md) | Bundled locales (`id_ID`, `en_US`) and how to add a new one. |

## Generators

| Page | What it covers |
|---|---|
| [Generator overview](generators/overview.md) | How tiers, chains, drops, upgrades, durability, and corruption fit together. |
| [Premade tier chains](generators/premade-chains.md) | What ships out of the box: `vanilla_chain.yml`, `mob_chain.yml`, `elemental_chain.yml`. |
| [Custom tiers](generators/custom-tiers.md) | Build your own chain in YAML — no Java required. |
| [Drops registry (`drops.yml`)](generators/drops-registry.md) | Material, weight, amount, presentation, price, craftability. |
| [Balance guide](generators/balance-guide.md) | Tweak tick interval, capacity, durability, repair cost, and shop price without breaking progression. |

## Integrations

| Page | What it covers |
|---|---|
| [Vault](integrations/vault.md) | Economy + permissions; what happens when Vault is missing. |
| [PlaceholderAPI](integrations/placeholderapi.md) | Using PAPI placeholders inside drop conditions, event triggers, and GUIs. |
| [Holograms](integrations/holograms.md) | DecentHolograms, FancyHolograms, HolographicDisplays, and native (display-entity) fallback. |
| [Protections](integrations/protections.md) | WorldGuard, GriefPrevention, Lands, Towny — auto-detection and override. |

## Operations

| Page | What it covers |
|---|---|
| [Backups](operations/backup.md) | Auto-snapshots, manual `/egens backup`, retention, restore. |
| [Performance](operations/performance.md) | Tick-jitter, async loot rolls, hologram refresh rate, profiler. |
| [Metrics (bStats)](operations/metrics.md) | What we report, how to opt out. |
| [Troubleshooting](operations/troubleshooting.md) | Common errors and their fixes. |
| [FAQ](operations/faq.md) | Frequently asked questions. |

---

## Support development

eGens is built by [@epromite](https://github.com/epromite). If the
plugin saves you time on your server, a one-time tip on Ko-fi is the
single best way to keep it maintained:

[![Tip on Ko-fi](https://img.shields.io/badge/Tip%20on%20ko--fi-Support%20eGens-ff5e5b?logo=ko-fi&logoColor=white)](https://ko-fi.com/epromite/tip)

## Need help?

- **Bug reports & feature requests** — Modrinth project page.
- **Chat** — <https://discord.gg/pM4atmCVS6>.
