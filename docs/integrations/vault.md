# Vault

[Vault](https://www.spigotmc.org/resources/vault.34315/) is the
standard economy abstraction for Bukkit/Paper. eGens auto-detects
Vault on boot.

## What Vault powers

| Feature | Falls back to |
|---|---|
| `/egens upgrade` (level + promotion) | Disabled — the player sees `command.upgrade.no-economy`. |
| `/egens repair` | Disabled — `command.repair.no-economy`. |
| `/egens shop` purchases | Disabled — the Shop tile in the hub renders a "feature offline" placeholder. |
| `/egens sell`, `/egens sellall`, `/sell`, `/sell all` | Disabled — `command.sell.not-sellable`. |
| Sellwand sales | Disabled — sellwand uses aren't decremented. |
| Sell-all unlock cost | Disabled. |

In short: without Vault eGens still places, ticks, and breaks
generators, but every money-touching action is gated.

## Switching providers

`config.yml`:

```yaml
integrations:
  vault: AUTO            # AUTO | INTERNAL
```

- `AUTO` — use Vault if present, otherwise fall back to the
  internal stub.
- `INTERNAL` — always use the stub. Useful when you've installed
  Vault for a different plugin and want eGens to behave as if no
  economy was available.

## Compatible economy plugins

Vault relays economy calls to whichever economy plugin you've
installed. Any Vault-compatible economy works:

- EssentialsX (`Essentials Economy`)
- CMI
- Treasury (via its Vault bridge)
- TheNewEconomy
- Custom in-house economies that register a `VaultEconomy` service

## Permissions

eGens also reads from Vault's permission abstraction when checking
`egens.addlimit.<n>` and the personal-event slot permissions. If
your permission plugin doesn't register with Vault, the plugin
falls back to Bukkit's `Player#hasPermission` — which works for
all standard permission plugins (LuckPerms, GroupManager, …) since
they hook into Bukkit's permission system directly.

## Currency formatting

Player-facing currency strings come from Vault
(`Economy#format(double)`). If your economy plugin returns ugly
strings (e.g. `$1234.5678`), configure the format inside the
economy plugin rather than in eGens — there is no per-eGens
override.

## Multi-currency

eGens uses a single Vault currency for every charge. If your
economy plugin exposes multiple currencies and only one is wired
as the "primary" Vault currency, that's the one eGens will use.
Multi-currency economies that don't expose a primary will surface
the first one — there's no way to pick a specific currency from
inside eGens today.

## Testing without Vault

To temporarily disable Vault interactions on a test server:

```yaml
integrations:
  vault: INTERNAL
```

Then reload. Every money command surfaces the "economy missing"
fallback message immediately, which is useful for verifying the
UX path.

---

[Tip on Ko-fi](https://ko-fi.com/epromite/tip) keeps maintenance going.
