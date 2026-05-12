# Metrics (bStats)

eGens reports anonymous usage stats via [bStats](https://bstats.org).
The data is aggregated across all servers running the plugin and
helps the author make better roadmap decisions (which integrations
are actually used, which database backends matter, etc.).

## What we send

| Metric | Example value | What it tells us |
|---|---|---|
| Server count | (one event per submitting server) | Total deployment scale. |
| Storage backend | `SQLITE` / `MYSQL` / `MARIADB` | How to prioritise backend-specific work. |
| Backup mode | `auto+manual`, `manual-only`, `disabled` | Which backup features are in active use. |
| Profiler enabled | `true` / `false` | Whether the profiler matters to real servers. |
| Hologram provider | `NATIVE` / `DECENT` / `FANCY` / `HD` / `disabled` | Which hologram libraries to keep adapters for. |
| Protection provider | `WORLDGUARD` / `GRIEFPREVENTION` / `LANDS` / `TOWNY` / `NONE` | Which protection adapters to keep healthy. |
| Server software | `Paper` / `Folia` | Folia-only feature gating decisions. |

That's it. eGens does **not** report:

- Player counts, player names, UUIDs, IP addresses.
- Tier definitions, drop tables, custom YAML content.
- Database contents, audit logs, generator coordinates.
- Plugin versions of other plugins beyond what bStats itself
  collects globally (server software, Java version, etc.).

The data flows directly to bStats — eGens itself doesn't run any
telemetry server.

## Opting out

There are two layers:

1. **Plugin-level** — `bstats.enabled: false` in `plugins/eGens/config.yml`.
2. **Server-wide** — `enabled: false` in `plugins/bStats/config.yml`.
   This opts every plugin out at once.

Either flip is sufficient. You don't need to do both.

## Verifying

After flipping the switch and reloading:

- The console will not print a bStats startup line for eGens.
- The bStats dashboard will see no new events from your server
  (delay: ~30 min for fresh data).

## bStats project ID

The bundled `config.yml` has `bstats.plugin-id: -1`, which
**skips submission entirely** so eGens can't accidentally pollute
someone else's bStats slot before it's registered. Once the
project is registered on bStats, the bundled config will ship the
real plugin id.

If you're on an older build where `plugin-id: -1` and you want to
participate in stats, update to a release that ships the
registered id — there's nothing you can configure by hand.

## Privacy stance

eGens treats player data as untouchable. The bStats integration
is the only outbound network traffic the plugin makes other than
the update check (`update-checker.source: MODRINTH`). Both are
opt-out via simple boolean switches.

If you have specific privacy or compliance requirements,
disabling both:

```yaml
bstats:
  enabled: false

update-checker:
  enabled: false
```

…produces a fully air-gapped plugin: no outbound network calls,
no telemetry, no version pings.

---

[Tip on Ko-fi](https://ko-fi.com/epromite/tip) keeps maintenance going.
