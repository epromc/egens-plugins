# Holograms

eGens renders a floating nameplate above every placed generator
showing the tier, level, and (optionally) durability. Four
backends are supported:

| Backend | Plugin |
|---|---|
| `DECENT` | [DecentHolograms](https://modrinth.com/plugin/decentholograms) |
| `FANCY` | [FancyHolograms](https://modrinth.com/plugin/fancyholograms) |
| `HD` | [HolographicDisplays](https://dev.bukkit.org/projects/holographic-displays) |
| `NATIVE` | Vanilla Minecraft display entities (no companion plugin needed). |

## Choosing a backend

`config.yml`:

```yaml
generator:
  hologram:
    enabled: true
    provider: AUTO          # AUTO | NATIVE | DECENT | FANCY | HD
    view-distance: 32
    show-level: true
    show-durability: false
    text-shadow: true
```

- `AUTO` probes installed plugins in this order:
  `DecentHolograms → FancyHolograms → HolographicDisplays` and
  falls back to `NATIVE` if none are present.
- `NATIVE` always uses the vanilla `TextDisplay` renderer.
- `DECENT` / `FANCY` / `HD` force the matching adapter even when
  the companion plugin is missing — the adapter degrades to a
  silent no-op plus a single startup warning so the misconfiguration
  is visible in logs without spamming.

## When to pick which

| Backend | Pros | Cons |
|---|---|---|
| `NATIVE` | Zero dependencies; fast and lightweight; works on Folia. | Less feature-rich (no animations, no per-line entities). |
| `DECENT` | Mature, lots of features. | One more plugin to install and update. |
| `FANCY` | Modern, actively maintained. | Newer — fewer guides online. |
| `HD` | Long-standing community standard. | Older API surface; some 1.21 servers find native faster. |

If you don't have a strong preference, leave `provider: AUTO` —
eGens picks the best available and gets out of the way.

## Display fields

| Field | Default | Notes |
|---|---|---|
| Tier line | always shown | Renders the tier's `display-name` from its `generators/*.yml` file. |
| Level line | on | Hide via `show-level: false`. |
| Durability line | off | Enable via `show-durability: true`. Useful when corruption is enabled and players want to see remaining hits. |

## Performance

- `performance.hologram-update-ticks` (in `config.yml`) sets the
  refresh interval. Default `20` (one second).
- `view-distance` is a hard cull radius. Holograms outside this
  range aren't sent to the player at all.
- Per-player visibility is honoured — players can hide all
  generator holograms via [`/egens settings`](../commands/player.md#egens-settings)
  (the per-player toggle is stored in their settings row).

## Customising the look

The nameplate template is built from MiniMessage strings keyed in
`lang/*.yml` (search for `hologram.` keys). You can:

- Tweak the colors / styles in your locale file.
- Change which lines appear by flipping `show-level` /
  `show-durability`.
- Override the tier display-name in the relevant `generators/*.yml`
  file.

There is no per-server "fully bespoke" hologram template — the
intent is consistency across servers running eGens. If you need a
radically different style, disable eGens' holograms
(`generator.hologram.enabled: false`) and render your own from a
companion plugin reading the generator's persistent-data tags.

## Troubleshooting

- **Hologram missing entirely** — check `generator.hologram.enabled`,
  verify the player is within `view-distance`, and check the
  per-player setting.
- **Hologram in wrong place** — happens if the world block was
  moved by another plugin without notifying eGens. Force a refresh
  by relogging or by running `/egens reload`.
- **Companion-plugin adapter logged a warning at startup** — the
  companion plugin is missing or hasn't enabled yet. Set
  `provider: AUTO` to fall back gracefully, or install the
  companion plugin.

---

[Tip on Ko-fi](https://ko-fi.com/epromite/tip) keeps maintenance going.
