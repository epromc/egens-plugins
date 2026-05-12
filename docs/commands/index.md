# Command index

eGens registers one root command `/egens` (aliases `/eg`, `/gens`)
plus a `/sell` shortcut. Every subcommand below is one click away
via `/egens help` in-game.

## At a glance

| Command | Permission | Player only? | Purpose |
|---|---|:---:|---|
| `/egens help` | — | no | Show every subcommand the caller can run. |
| `/egens menu` (`gui`, `open`) | `egens.use` | yes | Open the main hub GUI. |
| `/egens shop` | `egens.shop.use` | yes | Open the generator shop GUI. |
| `/egens info` | `egens.use` | yes | Show details about the generator you're looking at. |
| `/egens upgrade` | `egens.use` | yes | Quote and pay for an upgrade on the generator you're looking at. |
| `/egens repair` | `egens.use` | yes | Quote and pay for a repair on the generator you're looking at. |
| `/egens sell` | `egens.sell` | yes | Sell the item currently held in your main hand. |
| `/egens sellall` | `egens.sell` | yes | Sell every sellable item in your inventory. |
| `/sell` | `egens.sell` | yes | Convenience alias — `/sell` = `/egens sell`, `/sell all` = `/egens sellall`. |
| `/egens settings` | `egens.use` | yes | Open the personal settings GUI (locale, bossbar, etc). |
| `/egens give <player> <tier> [amount]` | `egens.admin.give` | no | Hand a tier's generator item to a player. |
| `/egens reload` | `egens.admin.reload` | no | Hot-reload `config.yml`, `events.yml`, GUIs, locales, generator chains. |
| `/egens backup` | `egens.admin.backup` | no | Trigger a manual database snapshot. |
| `/egens profile` (`perf`) | `egens.admin.perf` | no | Render the top-N profiler digest. |
| `/egens debug` | `egens.admin.debug` | no | Toggle verbose debug logging at runtime. |
| `/egens event <subcommand>` | `egens.admin.event` | no | Trigger / cancel server-wide and personal events. |
| `/egens sellwand <list|give>` | `egens.admin.sellwand` | no | Manage sellwand presets and hand them out. |

## See also

- [Admin commands — details](admin.md)
- [Player commands — details](player.md)

---

[Tip on Ko-fi](https://ko-fi.com/epromite/tip) keeps maintenance going.
