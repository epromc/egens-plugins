# Localization (`lang/*.yml`)

Every player-facing string in eGens is keyed and rendered through
MiniMessage. Locales live in `plugins/eGens/lang/`. Two are bundled:

- `id_ID.yml` — Bahasa Indonesia (default).
- `en_US.yml` — English (fallback for any key missing from the
  active locale).

## Picking the default

`config.yml`:

```yaml
locale:
  default: id_ID          # the fallback when a player has no setting
  available: [id_ID, en_US]
```

A player can change their personal locale via [`/egens settings`](../commands/player.md#egens-settings).

## Adding a new locale

1. Copy `en_US.yml` to `<new-tag>.yml` (e.g. `fr_FR.yml`).
2. Translate the values. Keys are stable across locales — never
   rename them.
3. Add the tag to `locale.available` in `config.yml`.
4. Run `/egens reload`.

### Naming

Use BCP-47 / Bukkit-style locale tags (`en_US`, `id_ID`, `fr_FR`,
`pt_BR`, `de_DE`, …). Hyphens (`en-US`) are not accepted — use
underscores.

## MiniMessage cheat sheet

All values are [MiniMessage](https://docs.advntr.dev/minimessage/).
Quick reminders:

- Colors: `<red>`, `<#9be7a7>`, `<gradient:#33ffaa:#3399ff>`.
- Styles: `<bold>`, `<italic>`, `<underlined>`, `<strikethrough>`,
  `<obfuscated>`.
- Placeholders: `<key>` where `key` is the placeholder name listed
  in the source string. Example:
  `command.give.success: "<prefix><#9be7a7>Gave</#9be7a7> <#ffd166><amount> <tier></#ffd166>"`
  has `amount` and `tier` as placeholders.
- Custom tag `<sc:Text>` converts the wrapped text to small-caps
  Unicode (ᴛᴇxᴛ) — used by the bundled style to mirror the GUI
  typography in chat.
- Legacy color codes (`&a`, `&l`, `&#FF6600`) are auto-translated
  on load.

## Fallback behaviour

When a key is missing from the active locale, eGens falls back to
`en_US`. If the key is also missing there, the literal key is
rendered (e.g. `command.give.success`) so the missing translation
is obvious without crashing anything.

## Style guide

The bundled defaults share a consistent palette so new keys feel
native:

| Element | MiniMessage tag |
|---|---|
| Prefix | `<gradient:#33ffaa:#3399ff><b>eGens</b></gradient>` |
| Body | `<#cfcfcf>` |
| Success accent | `<#9be7a7>` |
| Warning accent | `<#ffd166>` |
| Error accent | `<#ff8a8a>` |
| Muted | `<#7a7a7a>` |
| Section dim | `<#3a3a3a>` |

Stick to these when adding new locales for a coherent look across
chat + GUI + bossbars.

## Don't translate

A handful of strings are intentionally English:

- Permission node strings (`egens.admin.give`, …) — they're
  technical identifiers.
- The `prefix` line — its visual identity is part of the brand.
- Material ids inside the lore (e.g. `IRON_INGOT`) — they're
  resolved by the client based on its own locale.

---

[Tip on Ko-fi](https://ko-fi.com/epromite/tip) keeps maintenance going.
