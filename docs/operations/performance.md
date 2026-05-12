# Performance

eGens is designed to scale to hundreds of concurrently active
generators on a single Paper or Folia node. This page lists the
knobs that affect tick budget and a triage workflow for slow
servers.

## Tick budget knobs

`config.yml`:

```yaml
performance:
  tick-jitter: true
  async-loot-roll: true
  hologram-update-ticks: 20
```

| Knob | Effect |
|---|---|
| `performance.tick-jitter` | Spreads generator ticks across the second to avoid spike alignment. Leave `true` unless you have a very specific reason. |
| `performance.async-loot-roll` | Rolls the weighted loot table on a worker thread. The result is applied back on the region thread before the item is spawned. Leave `true`. |
| `performance.hologram-update-ticks` | Hologram refresh interval. `20` = once per second. Raise to `40` (every 2s) on busier servers — players won't notice. |

## Owner-activity gate

`config.yml`:

```yaml
generation:
  owner-activity:
    enabled: true
    max-distance-blocks: 64
```

This is the **biggest single performance win** on AFK-heavy
servers. When the owner is offline or further than
`max-distance-blocks` from the generator, the tick is skipped
entirely — no loot roll, no item spawn, no durability decay,
nothing.

Bump `max-distance-blocks` for "AFK farm" servers where you
*want* offline ticking; lower it for servers fighting AFK farms.

## Profiler

`config.yml`:

```yaml
profiler:
  enabled: false           # toggle at runtime with /egens debug or /egens profile
  top-n: 10
```

When enabled, the profiler accumulates wall-clock time per named
scope and `/egens profile` (alias `/egens perf`) renders a top-N
digest. Each scope records:

- Hit count.
- Total elapsed time.
- Max observed time.

The profiler adds zero overhead when disabled (the recording
methods are short-circuited at the call site).

### Recommended workflow

1. Notice a tick spike (TPS report, MSPT plot, player complaint).
2. `/egens profile reset` — wipe the accumulator.
3. Wait a few minutes for the spike to recur (or trigger it
   deliberately).
4. `/egens profile` — read the top-N digest. The hot scope is
   usually obvious from the total time column.
5. Flip `profiler.enabled: false` (or `/egens debug` if you
   toggled it that way) when done.

## bossbar throttling

Per-event bossbars are rate-limited to a single update per second.
You don't usually need to tune this — the overhead is dominated by
the network send to each player, not the eGens logic.

## Hologram performance

If you see hologram-related lag:

1. Increase `performance.hologram-update-ticks` from `20` to `40`
   or `80`.
2. Lower `generator.hologram.view-distance` from `32` to `16`.
3. Switch to `provider: NATIVE` — vanilla `TextDisplay` entities
   are faster than most companion plugins.
4. Disable holograms entirely (`generator.hologram.enabled: false`)
   on very large servers; the generator block itself is enough
   visual identity.

## Database

- **SQLite + Paper** — fine up to a few hundred concurrent players.
  Beyond that, WAL contention starts to bite.
- **MySQL + Paper / Folia** — recommended for >500 concurrent
  players or multi-instance networks.
- **HikariCP pool size** — `storage.mysql.pool-size: 10` is the
  default. Raise to 20–30 for very busy servers.

The audit log writes are batched (`anti-dupe.flush-interval-ticks:
200` = once every 10 seconds). Raising this is usually unnecessary
— the disk impact is minimal.

## Folia notes

eGens is fully Folia-aware. Generator ticks are scheduled per
region; the owner-activity gate works at the region thread level.
There's no global "tick loop" that competes for the main thread —
each region scales independently.

The few things that **do** run on the global thread:

- Periodic backup snapshots (network/disk I/O is offloaded but
  the scheduler hook is global).
- Periodic config reloads (only when triggered via `/egens reload`).
- The hologram refresh tick on the `NATIVE` provider, which is
  bounded by `view-distance` and `hologram-update-ticks`.

## Single-node sizing guide

| Concurrent active players | Recommended setup |
|---|---|
| < 50 | SQLite. Defaults. |
| 50 – 200 | MySQL local. Defaults. Raise `hologram-update-ticks` to `40`. |
| 200 – 500 | MySQL local (or LAN). `pool-size: 20`. Enable profiler during launches. |
| 500+ | Folia + MySQL on a separate host. `pool-size: 30`. Consider `hologram.enabled: false` on the busiest worlds. |

---

[Tip on Ko-fi](https://ko-fi.com/epromite/tip) keeps maintenance going.
