# Admin commands

All admin subcommands gate behind a single leaf permission per
command (see the table in [Permissions](../getting-started/permissions.md)).
`egens.admin` is the catch-all parent for staff.

## `/egens give <player> <tier> [amount]`

Hand a placeable generator item to a player.

- **Permission:** `egens.admin.give`
- **Console-safe:** yes (target must be online).
- **Tab completion:** suggests online players and known tier ids
  (from every loaded `generators/*.yml`).

Example:

```
/egens give Notch wood 4
```

The item lands in the target's inventory or drops at their feet if
they're full. The audit log records who issued the command, the
tier id, and the amount.

## `/egens reload`

Hot-reloads every YAML config in `plugins/eGens/`:

- `config.yml`
- `events.yml`
- `sellwands.yml`
- `gui/*.yml`
- `lang/*.yml`
- `generators/*.yml` (chains and drops)

- **Permission:** `egens.admin.reload`
- **Console-safe:** yes.

The reload is incremental — running services (database connections,
schedulers, hologram refreshers, the backup timer) are reconfigured
in place rather than restarted. Players don't notice. If a config
file fails to parse the reload aborts cleanly and the in-memory
state stays on the last good version; the failure is logged.

> A handful of switches **can't** be hot-reloaded — primarily the
> storage backend (`storage.type` and credentials). Changing those
> requires a full server restart.

## `/egens backup`

Trigger a database snapshot on demand. See
[Backups](../operations/backup.md) for the full mechanic.

- **Permission:** `egens.admin.backup`
- **Console-safe:** yes.

The snapshot writes to `plugins/eGens/backups/` and respects the
`backup.retention-count` cap — older snapshots are pruned after a
successful run.

## `/egens profile` (alias `/egens perf`)

Render the top-N profiler digest sorted by total time spent.

- **Permission:** `egens.admin.perf`
- **Console-safe:** yes.

Useful when investigating tick spikes. The profiler is opt-in
(`profiler.enabled: true` in `config.yml`) and adds zero overhead
when disabled.

Subcommands:

- `/egens profile` — show the top entries (default 10 — configurable
  via `profiler.top-n`).
- `/egens profile reset` — wipe the accumulator so a fresh sample
  window starts.

See [Performance](../operations/performance.md) for more on the
profiler.

## `/egens debug`

Toggle verbose debug logging at runtime.

- **Permission:** `egens.admin.debug`
- **Console-safe:** yes.

Equivalent to flipping `debug: true` in `config.yml` and reloading,
but without rewriting the file. Use it when reproducing a bug —
turn it on, reproduce, turn it off, send the console log.

## `/egens event <subcommand>`

Manage generator events.

- **Permission:** `egens.admin.event`
- **Console-safe:** yes.

Common subcommands:

- `/egens event list` — list every event id from `events.yml` and
  whether it's currently active.
- `/egens event start <id> [duration]` — start a server-wide event.
  Duration overrides the value in `events.yml`.
- `/egens event stop <id>` — cancel an active event early.
- `/egens event grant <player> <id> [duration]` — grant a personal
  event to a single player.

See [`events.yml`](../config/events-yml.md) for the event types.

## `/egens sellwand <subcommand>`

Manage sellwand presets.

- **Permission:** `egens.admin.sellwand`

Subcommands:

- `/egens sellwand list` — list every preset from `sellwands.yml`
  along with its material, multiplier, and default-uses count.
- `/egens sellwand give <player> <preset> [uses]` — hand a sellwand
  to a player. `uses` defaults to the preset's `default-uses`.

The wand uuid is tracked in the database so duplicating it via
inventory exploits won't multiply uses — every sale debits the
canonical row.

---

[Tip on Ko-fi](https://ko-fi.com/epromite/tip) keeps maintenance going.
