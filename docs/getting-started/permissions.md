# Permissions

eGens registers a small, predictable permission tree under the
`egens.*` prefix. Player-facing nodes default to `true` so a
brand-new install works without any permission setup; admin nodes
default to `op` (or `false`) so OPs can do everything and ordinary
players can't.

## Player nodes

| Node | Default | Grants |
|---|---|---|
| `egens.use` | `true` | Base permission for using player-facing eGens features. |
| `egens.shop` (parent) | `true` | Inherits `egens.shop.use`. |
| `egens.shop.use` | `true` | `/egens shop` and the Shop tile in the main hub. |
| `egens.sell` | `true` | `/sell`, `/egens sell`, `/egens sellall`. |
| `egens.limit.bypass` | `false` | Bypass per-player and per-chunk generator limits. |
| `egens.world.bypass` | `false` | Bypass the world whitelist/blacklist when placing generators. |

The "limit additive" prefix (default `egens.addlimit.`) lets you
give a player extra generator slots. Example: `egens.addlimit.25`
adds 25 to whatever the configured base limit is. The prefix is
configurable via `limits.additive-permission-prefix`.

## Admin nodes

| Node | Default | Grants |
|---|---|---|
| `egens.admin` (parent) | `op` | Inherits every node below. |
| `egens.admin.bypass` | `false` | Bypass owner-only checks (staff override). |
| `egens.admin.give` | `false` | `/egens give`. |
| `egens.admin.reload` | `false` | `/egens reload`. |
| `egens.admin.perf` | `false` | `/egens profile` / `/egens perf`. |
| `egens.admin.backup` | `false` | `/egens backup`. |
| `egens.admin.debug` | `false` | `/egens debug`. |
| `egens.admin.sellwand` | `false` | `/egens sellwand` admin subcommands. |
| `egens.admin.event` | `false` | `/egens event` admin subcommands. |
| `egens.admin.update` | `false` | Receive update notifications on join. |
| `egens.admin.break` | `false` | Break any eGens generator regardless of ownership. |
| `egens.admin.open.others` | `false` | Open another player's generator GUI (admin override). |

`egens.admin` is the easy shortcut for staff. Grant it once and the
admin gets every leaf node above. If you want to grant only a
subset (e.g. a builder team that can reload configs but can't
generate items), grant the leaf nodes individually.

## LuckPerms snippets

```
/lp group default permission set egens.use true
/lp group default permission set egens.shop.use true
/lp group default permission set egens.sell true

/lp group staff permission set egens.admin.reload true
/lp group staff permission set egens.admin.backup true
/lp group staff permission set egens.admin.perf true

/lp group owner permission set egens.admin true

/lp user <player> permission set egens.addlimit.25 true
/lp user <player> permission set egens.limit.bypass true
```

## Permission errors

When a permission check fails the player sees the message keyed by
`command.no-permission` in [`lang/*.yml`](../config/lang.md). The
console doesn't log the failure — by design — so you don't get
spammed every time a player tabs through commands they can't run.

---

[Tip on Ko-fi](https://ko-fi.com/epromite/tip) keeps maintenance going.
