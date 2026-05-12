# PlaceholderAPI

[PlaceholderAPI](https://www.spigotmc.org/resources/placeholderapi.6245/)
(PAPI) is auto-detected at boot. eGens uses PAPI as a **consumer**
— other plugins' placeholders are resolved inside eGens config
files. Common uses:

- Drop conditions in `drops.yml` that check player state.
- Event triggers that need to read game state.
- GUI titles / lore that include cross-plugin placeholders.

## Master switch

`config.yml`:

```yaml
integrations:
  placeholderapi: AUTO    # AUTO | INTERNAL
```

- `AUTO` — use PAPI if present, otherwise treat every `%placeholder%`
  reference as the literal string.
- `INTERNAL` — always treat as literal, even when PAPI is loaded.
  Useful for sandboxed test setups.

## Placeholder syntax

Any string field in `drops.yml`, `events.yml`, or `gui/*.yml` that
accepts MiniMessage also accepts `%plugin_placeholder%` tokens.
PAPI expands them at render time using the current player as the
context.

Example: a drop only available to players in a specific party
plugin:

```yaml
drops:
  party_bonus:
    item: EMERALD
    weight: 50
    min: 1
    max: 2
    price: 100
    condition: "%party_in_party% == true"   # consumed by drop-condition evaluator
```

## Operator support

The drop-condition evaluator supports the following operators,
listed in order of evaluation:

```
>=    <=    !=    ==    =    >    <
```

`==` and `=` are equivalent. `!=` is "not equal". `>`, `<`, `>=`,
`<=` do numeric comparison when both sides parse as numbers.
String comparison is fallback when either side isn't a number.

When no operator is present, the condition is a "truthy" check —
it passes if the resolved string is non-blank, non-`false`,
non-`0`, and non-`no`.

## Reading eGens state via PAPI

eGens itself does **not** register a `%egens_*%` placeholder
expansion in the current release. Other plugins that want to read
eGens state should query the database directly (read-only) or
listen for the events eGens fires. A registered placeholder
expansion may be added in a future release once the placeholder
surface stabilises — feedback welcome via the project's Modrinth
page.

## Testing placeholder conditions

The easiest way to verify a condition works:

1. Set `debug: true` in `config.yml` (or run `/egens debug`).
2. Stand near a generator that uses the conditional drop.
3. Tail the console — every roll prints the resolved condition
   expression and its outcome.

Toggle `debug` back off when done; it's chatty.

## Performance

PlaceholderAPI is a fast plugin. eGens caches the resolved value
of a placeholder inside the scope of a single drop roll, so a
condition like `%party_in_party% == true` resolves the placeholder
once per tick, not once per drop entry.

If you're piping a particularly expensive placeholder (one that
hits a database every call), measure with `/egens profile` after a
few minutes of play.

---

[Tip on Ko-fi](https://ko-fi.com/epromite/tip) keeps maintenance going.
