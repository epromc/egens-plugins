# `events.yml`

`events.yml` defines every server-wide generator event admins can
trigger via `/egens event start <id>` or the auto-rotation. Personal
events use the same schema — they're granted to a single player via
`/egens event grant`.

The bundled file ships with five canonical events. Add your own
entries under the same `events:` map.

## Event schema

```yaml
events:
  <event-id>:
    type: SELL_MULTIPLIER         # required
    duration-minutes: 10          # OR duration-seconds: 600
    multiplier: 2.0
    display-name: "<gradient:#ffd166:#ff8a3d><bold>Double Profit</bold></gradient>"
    description:
      - "<#cfcfcf>Every <yellow>/sell</yellow> payout is doubled."
    bossbar-color: YELLOW
```

| Field | Required | Notes |
|---|---|---|
| `type` | yes | One of the event types below. |
| `duration-minutes` | one of | Human-friendly duration. |
| `duration-seconds` | one of | Overrides `duration-minutes` when present. |
| `multiplier` | varies | Used by `SELL_MULTIPLIER`, `DROP_AMOUNT`, `SPEED_BOOST`. Ignored by `DROP_TIER` / `MIXED_UP`. |
| `display-name` | yes | MiniMessage. Legacy color codes (`&a`, `&l`, `&#FF6600`) auto-translated. |
| `description` | yes | List of MiniMessage lines for the bossbar tooltip / event-inbox card. |
| `bossbar-color` | yes | `PINK`, `BLUE`, `RED`, `GREEN`, `YELLOW`, `PURPLE`, `WHITE`. |

## Event types

| Type | Effect |
|---|---|
| `SELL_MULTIPLIER` | Every `/sell`, `/sellall`, and sellwand payout is multiplied by `multiplier`. Stacks orthogonally with player multipliers. |
| `DROP_AMOUNT` | Every drop produced by a generator is scaled by `multiplier` (rounded **up** so a fractional bonus produces at least one extra item). |
| `DROP_TIER` | Generators temporarily roll the **next-tier** loot table. Top-tier generators stay on their own table. `multiplier` is ignored. |
| `SPEED_BOOST` | Generators fire `generateLoot()` `multiplier` times per scheduled tick. Capped at `5` so a misconfigured value can't melt a region thread. |
| `MIXED_UP` | Every tick, each generator rolls a **random** registered tier's loot table. Could be dirt rolling Diamond loot — or vice versa. `multiplier` ignored. |

## Stacking behaviour

Multiple server-wide events can run concurrently when
`events.stack.max-global` is `> 1` in [`config.yml`](config-yml.md).
Stacking is **additive over the baseline**:

```
effective = 1 + Σ(multiplier_i - 1)
```

So a 2× and a 3× event stacked together give 4× (not 6×).

| Type | Stack rule |
|---|---|
| `SELL_MULTIPLIER` | Additive (see formula above). |
| `DROP_AMOUNT` | Additive. |
| `SPEED_BOOST` | Uses `max` instead — additive would overload the region thread. |
| `DROP_TIER` | Idempotent — any one active grant promotes the loot tier. |
| `MIXED_UP` | Idempotent. |

When the stack is at capacity and an admin starts another event,
the new event refreshes any matching id in place; if the id is
new, the **oldest** active event is evicted in its favour.

## Personal events

Personal events share the same `events.yml` definitions but get
granted per-player via `/egens event grant <player> <id>`. The
slot cap is governed by `events.personal.slots.default` plus the
permission `egens.personal.slots.<n>` (highest matching node
wins, bounded by `events.personal.slots.max-cap`).

## Auto-rotation

When `events.auto-trigger.enabled: true` (the default), the
scheduler picks a random event from `events.auto-trigger.pool`
every `events.auto-trigger.interval-minutes` minutes and starts it
server-wide. Remove an event id from the pool to exclude it from
the rotation without deleting the definition.

## Bundled events

| id | Type | Duration | Multiplier |
|---|---|---|---|
| `sell_multi` | `SELL_MULTIPLIER` | 10 min | 2.0× |
| `drop_amount` | `DROP_AMOUNT` | 10 min | 1.5× |
| `drop_tier` | `DROP_TIER` | 5 min | — |
| `speed_boost` | `SPEED_BOOST` | 5 min | 2.0× |
| `mixed_up` | `MIXED_UP` | 5 min | — |

---

[Tip on Ko-fi](https://ko-fi.com/epromite/tip) keeps maintenance going.
