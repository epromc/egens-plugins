# Player commands

Player commands are usable by anyone with `egens.use` (granted by
default to everyone). The shop and selling commands have their own
parent permissions; you can revoke either without touching the
rest.

## `/egens menu` (aliases `gui`, `open`)

Opens the main hub GUI — the inventory layout defined by
[`gui/main_menu.yml`](../config/gui-yml.md).

- **Permission:** `egens.use`
- **Player-only:** yes.

The hub is the discoverability surface — players click the Shop
tile to open the shop, the Settings tile to open
[`/egens settings`](#egens-settings), the Sellwand tile to see their
sellwand uses, and so on. Customise the icons and labels via
`gui/main_menu.yml`.

## `/egens shop`

Opens the generator shop GUI — the inventory layout defined by
[`gui/shop.yml`](../config/gui-yml.md).

- **Permission:** `egens.shop.use`
- **Player-only:** yes.

Players see every tier they're allowed to buy. Pricing is read
from each tier's `shop-price` in its `generators/*.yml` file. The
shop provider is pluggable: by default it uses an internal
implementation, but you can route purchases through
**ShopGUIPlus** or **EconomyShopGUI** via `integrations.shop-provider`.

## `/egens info`

Show details about the generator block the player is currently
looking at.

- **Permission:** `egens.use`
- **Player-only:** yes.
- **Range:** controlled by `commands.target-range` (default `6`
  blocks).

Output is a single chat line: tier display name · current level /
max level · current durability / max durability · corrupted flag.
The localized template is `command.info.body` in `lang/*.yml`.

## `/egens upgrade`

Quote and pay for an upgrade on the generator the player is
looking at.

- **Permission:** `egens.use`
- **Player-only:** yes.

Two upgrade kinds are quoted automatically:

- **Level upgrade** — bumps the generator's level within the
  current tier (improves drop weights / quantities according to the
  tier's loot table).
- **Tier promotion** — when the generator is at max level and the
  tier has a `next-tier:` pointer, the upgrade promotes the
  generator to the next tier.

The first call without arguments shows the quote; the second call
with the matching kind argument confirms and charges. Costs come
from `upgrade.level.cost-formula` and `upgrade.promotion.cost-formula`
in `config.yml`.

## `/egens repair`

Quote and pay for a repair on the generator the player is looking
at.

- **Permission:** `egens.use`
- **Player-only:** yes.

If the generator is at full durability, the command returns the
`command.repair.no-op` message. Cooldown and cost come from
`corruption.repair.*` in `config.yml`.

## `/egens sell` and `/sell`

Sell the item currently in your main hand.

- **Permission:** `egens.sell`
- **Player-only:** yes.

The sale price is the `price:` field of the matching entry in
`generators/drops.yml`. Items that aren't in the drop registry
return `command.sell.not-sellable`. The `/sell` shortcut is
provided so you can bind it to a button without nesting under
`/egens`.

## `/egens sellall` and `/sell all`

Sell every sellable item in your inventory in one go.

- **Permission:** `egens.sell`
- **Player-only:** yes.
- **Gated by:** the per-generator (or per-player) sell-all unlock
  cost — see `sell-all.unlock-cost` in `config.yml`.

The unlock scope is configurable:

- `PER_GENERATOR` — each generator has its own unlock state. The
  player has to unlock sell-all separately for every generator they
  own.
- `PER_PLAYER` — once unlocked, sell-all works for every generator
  the player owns.

## `/egens settings`

Opens the personal settings GUI — the inventory layout defined by
[`gui/settings.yml`](../config/gui-yml.md).

- **Permission:** `egens.use`
- **Player-only:** yes.

Players toggle their preferred locale, opt out of bossbar
notifications, and so on. Settings persist in the database
(`egens_user_settings` table) and survive server restarts.

## `/egens help`

Show every subcommand the caller can run. The visible list is
filtered by permission — players never see admin commands they
can't use.

---

[Tip on Ko-fi](https://ko-fi.com/epromite/tip) keeps maintenance going.
