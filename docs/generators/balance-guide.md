# Balance guide

Generator economy revolves around five knobs per tier:

| Knob | What it controls | Bundled curve |
|---|---|---|
| `tick-interval` (ms) | How often the generator fires. | Decreases ~9% per tier. |
| `base-capacity` | Loosely interpreted "yield" multiplier. | Increases ~1.55× per tier. |
| `base-durability` | Hit budget before corruption (in `DURABILITY` / `HYBRID` mode). | Increases ~1.4× per tier. |
| `shop-price` | Cost to buy a placeable. | Increases ~2.2× per tier. |
| `repair-cost` + `repair-cost-per-level` | Cost to repair. | Tied loosely to `shop-price`. |

These five values **together** define how rewarding a tier feels.
Tuning any one in isolation usually breaks the curve. This page is
a checklist for keeping the relationship sane.

## The implicit objective

The shop price for tier *n+1* should pay back in a reasonable
number of ticks at tier *n* — empirically around 2,000–5,000 ticks
of the previous tier feels right. Faster than 2k feels trivial;
slower than 5k feels grindy.

If you halve the bundled tick interval at tier *n* but don't touch
shop-price at tier *n+1*, you've roughly halved the payback time
without intending to. Always think in pairs:

- **Faster tier *n*** → bump `shop-price` of *n+1*.
- **Bigger drop weights at tier *n*** → bump `shop-price` of *n+1*.
- **Cheaper levels at tier *n*** → bump `shop-price` of *n+1* and
  level cost of *n+1*.

## Suggested progression curve

If you're authoring a new chain from scratch, this geometric
progression is a sensible starting point:

```
tick_interval(n)  = round(2400 * 0.91^n)        # ~9% faster per tier
base_capacity(n)  = round(500  * 1.55^n)
base_durability(n)= round(75   * 1.40^n)
shop_price(n)     = round(2000 * 2.20^n)
repair_cost(n)    = round(1000 * 2.20^n)
```

(`n` is the 0-based tier ordinal — `wood = 0`, `stone = 1`, …)

The bundled chains follow this curve; the off-tree generator
script `tools/gen_chains.py` in the plugin source repo writes the
files with the same formulas. Adjust the multipliers (0.91, 1.55,
1.40, 2.20) to taste.

## Upgrade-cost formula

`config.yml` keeps the upgrade pricing in two formulas:

```yaml
upgrade:
  level:
    cost-formula: "500 * level^1.8"
  promotion:
    cost-formula: "10000 * tier"
```

`level` is the source level when quoting; `tier` is the 1-based
ordinal of the destination tier in load order. The defaults are
deliberately on the steep side — level 10 within a tier costs
`500 × 10^1.8 ≈ 31,500`, which is enough to make the player
*choose* between levelling-up vs. promoting.

If you raise `max-level`, the level-cost formula should grow
faster (raise the exponent) so the late levels still bite.

## Repair-cost formula

`config.yml`:

```yaml
corruption:
  repair:
    cost-formula: "12.5 * tier"
```

Cheap by design — repairs should be an annoyance, not an
economy-driver. A diamond tier (tier 8) costs ~100 to repair under
the default. Bump the constant if you want repair to feel like a
real cost.

## Drop weights

The default `drops.yml` puts the high-weight item (e.g.
`iron_ingot: weight 80`) much higher than the side drops
(`coal: weight 20`). Keep at least one "anchor" drop in every
tier's loot table — that's the primary income flow — and use side
drops for flavour.

A common mistake is making every drop equally weighted. The
loot panel ends up looking impressive (eight items @ 12.5% each)
but the *average* income per tick is much smoother than the player
expects, which makes the tier feel like noise instead of progress.

## Event scaling

Events stack with the per-tier balance:

- `SELL_MULTIPLIER` multiplies the final sale price.
- `DROP_AMOUNT` multiplies the stack size produced per tick.
- `SPEED_BOOST` multiplies the number of `generateLoot()` calls
  per tick (hard-capped at 5).
- `DROP_TIER` temporarily promotes loot to the next tier's table.

If you author an event with `multiplier: 5.0`, every income line
on every generator gets a 5× window. Long durations (≥10 minutes)
plus big multipliers can rewrite your weekly economy — test on a
staging server first.

## Sell-all unlock cost

`config.yml`:

```yaml
sell-all:
  unlock-cost:
    type: MONEY
    amount: 5000
```

Tune this against your earliest sellable tier. If `wood` can be
purchased for 2,000 and yields ~5/tick at 2.4s, unlocking sell-all
at 5,000 takes ~40 minutes of wood-only play. That's about right
for the QoL-vs-cost trade-off.

## Quick sanity check

Once you've tuned your chain, eyeball these ratios:

| Ratio | Healthy range |
|---|---|
| `tier(n+1).shop-price / sum(tier(n) drop revenue per tick × 2000)` | 0.5 – 1.5 |
| `tier.upgrade.level (max-level cost) / tier.shop-price` | 5 – 20 |
| `tier.repair-cost / tier.shop-price` | 0.4 – 0.8 |

Numbers outside those bands usually feel "wrong" to players — too
trivial, too grindy, or off-pattern.

---

[Tip on Ko-fi](https://ko-fi.com/epromite/tip) keeps maintenance going.
