# `gui/*.yml`

Inventory layouts live under `plugins/eGens/gui/`. Each file
describes one menu and is hot-reloaded via `/egens reload`.

| File | Menu |
|---|---|
| `main_menu.yml` | The hub opened by `/egens menu` (aliases `gui`, `open`). |
| `shop.yml` | The generator shop opened by `/egens shop`. |
| `shop_confirm.yml` | The purchase-confirmation dialog before money is debited. |
| `settings.yml` | The per-player settings menu opened by `/egens settings`. |
| `generator.yml` | The generator-block menu shown when a player right-clicks a placed generator. |
| `generator-broken.yml` | The dialog shown when interacting with a corrupted generator. |
| `event-inbox.yml` | The personal-event inbox shown from the hub. |

## Layout DSL

Every menu uses the same character-coded grid + `items:` block
pattern:

```yaml
type: CHEST
title: "<dark_gray>eGens <gray>— <#9be7a7><sc:Shop>"
rows: 6

layout:
  - "BBBBBBBBB"
  - "BBBBBBBBB"
  - "BBBBBBBBB"
  - "BBBBBBBBB"
  - "BBBBBBBBB"
  - "P###H###N"

items:
  '#':
    material: BLACK_STAINED_GLASS_PANE
    name: " "
  'B':
    action: shop-buy
  'P':
    action: shop-prev
    material: ARROW
    name: "<gray>« Previous"
  'N':
    action: shop-next
    material: ARROW
    name: "<gray>Next »"
  'H':
    action: shop-hub
    material: COMPASS
    name: "<#9be7a7>Back to hub"
```

### Fields

| Field | Required | Notes |
|---|---|---|
| `type` | yes | Inventory type. `CHEST` is the only one you should need for a standard layout. |
| `title` | yes | MiniMessage. `<tier>` and `<tier-display>` are available inside per-generator menus. |
| `rows` | yes | `1`–`6`. |
| `layout` | yes | One string per row. Each character indexes into `items`. |
| `items` | yes | Map from character to item descriptor. |

### Item descriptor

| Key | Notes |
|---|---|
| `action` | Symbolic action id that wires this slot to a click handler (see the action table per menu). Slots without an action are static. |
| `material` | Bukkit material id. Optional — most action slots have built-in icons. |
| `name` | MiniMessage display-name. Optional. |
| `lore` | List of MiniMessage lines. Optional. |
| `glow` | `true` to apply the enchanted-glow effect (no enchantments attached). |
| `model-data` | Vanilla `CustomModelData` int for resource-pack overrides. |

## Action ids per menu

Slot codes used internally — the icon character (`L`, `S`, …) is
arbitrary. Only the `action` value matters.

### `main_menu.yml`

| Action | Effect |
|---|---|
| `gens-list` | Opens the player's generator overview. |
| `event-inbox` | Opens the event inbox. Renders a "feature offline" tile when the personal-events backend is unavailable. |
| `shop` | Opens the shop. Renders a "feature offline" tile when Vault economy is missing. |
| `settings` | Opens the personal settings GUI. |

### `shop.yml`

| Action | Effect |
|---|---|
| `shop-buy` | Each `shop-buy` slot renders one buyable generator tier sorted by price ascending. |
| `shop-prev` | Previous-page arrow (auto-hidden on page 1). |
| `shop-next` | Next-page arrow (auto-hidden on the last page). |
| `shop-info` | Page indicator (`Page 1 / 3 · 12 items`). |
| `shop-hub` | Back-to-hub button. |

### `shop_confirm.yml`

| Action | Effect |
|---|---|
| `confirm` | Charges the player and grants the generator item. |
| `cancel` | Returns to the shop. |

### `settings.yml`

| Action | Effect |
|---|---|
| `toggle-hologram` | Toggle floating nameplate visibility. |
| `toggle-sound` | Toggle generator sound effects. |
| `toggle-bossbar` | Toggle the event bossbar. |
| `pick-locale` | Cycle bundled locales. |

### `generator.yml`

| Action | Effect |
|---|---|
| `upgrade-level` | Triggers the level-upgrade quote/charge flow. |
| `upgrade-tier` | Triggers the tier-promotion quote/charge flow. |
| `repair` | Triggers the repair quote/charge flow. |
| `unlock-sell-all` | Triggers the sell-all unlock flow when scope is `PER_GENERATOR`. |
| `drop-list` | Renders the loot table summary (chances driven by `gui.drop-display`). |

## Tips

- Run `/egens reload` after edits — GUIs are reloaded incrementally.
- Don't reuse the same character for two different actions in one
  file; the last definition wins.
- If a slot rendering looks "off" in-game, double-check the
  `material` is a valid 1.21.x material id. Mojang occasionally
  renames materials between minor versions — sticking to common ids
  (`BARRIER`, `COMPASS`, `ARROW`, `STAINED_GLASS_PANE` variants) is
  the safest path.

---

[Tip on Ko-fi](https://ko-fi.com/epromite/tip) keeps maintenance going.
