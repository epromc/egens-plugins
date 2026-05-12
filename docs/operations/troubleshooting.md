# Troubleshooting

A grab-bag of common issues and the fastest fix. If your problem
isn't here, the first thing to try is `debug: true` (or `/egens
debug`) and tail the console while reproducing.

## Plugin won't enable

**Symptom:** `[eGens] Disabling plugin` on startup or `eGens
v1.0.0 failed to load`.

Likely causes:

- **Wrong Java version.** eGens requires Java 21. Run
  `java -version` on the host; anything older fails to load.
- **Wrong server software.** eGens needs Paper or Folia.
  Spigot/CraftBukkit are not supported.
- **MySQL credentials wrong.** Check the stack trace for a JDBC
  exception. Test the credentials manually with
  `mysql -h <host> -u <user> -p <database>`.
- **MySQL DDL permission missing.** The plugin runs migrations on
  boot. Grant the eGens user the ability to create tables and
  indexes: `GRANT ALL PRIVILEGES ON egens.* TO 'egens'@'%';`.

## Generators don't drop anything

Likely causes:

- **Owner offline / too far away.** The owner-activity gate
  (`generation.owner-activity`) skips ticks when the owner is
  offline or out of range. Disable it (`enabled: false`) for
  diagnostic purposes.
- **World blacklisted.** Check `worlds.mode` and `worlds.list` in
  `config.yml`. A blacklisted world will silently pause every
  generator placed in it.
- **Generator corrupted.** Look for the `cracked-material` block
  visual swap. Run `/egens repair` to fix.
- **Drop ids don't exist.** A typo in a chain's `drops:` list
  causes those drops to be skipped at runtime. Look for warnings
  like `[eGens] Unknown drop id 'iron_ingotz' …` in the startup
  log.

## "License required" / generators paused

eGens has no licensing feature today — you should never see this
state in the bundled plugin. If you see something that looks like
it, you're either running a fork or an old development build. Ask
on the Discord for help identifying the build.

## Sell command says "Nothing in your hand to sell"

The item in your main hand has no entry in `drops.yml` with a
matching `drop_id` stamp. Sellable drops are only those produced
by eGens generators (or minted with the right PDC). Vanilla
materials don't sell unless you've added them to `drops.yml`
*and* re-rolled them through a generator (or via a custom
plugin that stamps the PDC).

## Shop says "feature offline"

The Vault economy is missing. Install Vault + a Vault-compatible
economy plugin (EssentialsX, CMI, etc.) or set
`integrations.shop-provider: SHOPGUIPLUS` / `ECONOMYSHOPGUI`
to delegate the shop.

## Holograms missing / floating in wrong spot

- **Missing entirely** — verify `generator.hologram.enabled: true`,
  player setting hasn't hidden them, player is within
  `view-distance`.
- **Wrong location** — another plugin moved the world block
  without telling eGens. Relog or `/egens reload` to refresh.
- **Adapter warning at startup** — the named hologram provider's
  companion plugin is missing. Switch to `provider: AUTO` to fall
  back gracefully.

## `/egens reload` reports "config parse error"

The reload aborted cleanly — your in-memory state is still the
last good version. The console trace points at the offending file
and line. Fix the YAML, run `/egens reload` again.

Common YAML mistakes:

- Tabs instead of spaces (YAML doesn't accept tabs).
- Mismatched quote types (`'…"` etc.).
- Lost colon after a key (`material IRON_INGOT` instead of
  `material: IRON_INGOT`).
- MiniMessage with unclosed tags (`<gradient:#xxx:#yyy>…` without
  the matching close).

## `/egens backup` returns Failure

- **SQLite** — disk full, permission denied on the backup
  directory, or the database file was deleted out from under the
  plugin. Check the error tail in the response.
- **MySQL** — `mysqldump` missing on `PATH`, or the eGens MySQL
  user lacks the necessary privileges (needs `SELECT`, `LOCK
  TABLES`, `SHOW VIEW`, `EVENT`, `TRIGGER`). The error tail
  includes the stderr from `mysqldump`.

## Migration failed at boot

The migration runner is transactional — a failed migration rolls
back. Don't keep rebooting. Capture the full stack trace and:

1. Take a fresh database snapshot (or copy the SQLite file)
   *before* anything else.
2. Report the failure on the Discord or the plugin's Modrinth
   page with the trace.
3. Roll back to the previous plugin version *and* the corresponding
   snapshot; don't run a newer plugin against a partially-migrated
   database.

## Performance degraded after upgrade

- Check `/egens profile` after a few minutes of play to identify
  the hot path.
- Verify `generation.owner-activity` is still enabled — disabling
  it causes a massive load increase on AFK farms.
- Verify your database isn't the bottleneck — `pool-size` may need
  to be raised; see [Performance](performance.md).

## Players bypassing limits

- Check whether the player has `egens.limit.bypass`.
- Check whether they have `egens.addlimit.<n>` for a large `n` —
  the highest matching suffix wins.
- Audit `limits.per-world.<world>` overrides — a per-world cap of
  `-1` is uncapped.

## Sellwand uses don't decrement

- Confirm `sellwand.enabled: true` in `config.yml`.
- Confirm the wand was minted *after* the most recent reload —
  pre-reload wands keep their original multiplier and the use
  count tracked on the row.
- Look at the audit log for `WAND_DESYNC` entries — a cloned wand
  hits this every other sale.

## Still stuck?

Drop a console log + `config.yml` + the offending chain/drops file
in the Discord (https://discord.gg/pM4atmCVS6) or open an issue
on Modrinth. Include the plugin version (`/version eGens`) and
the server software version (`/version Paper` or `/version Folia`).

---

[Tip on Ko-fi](https://ko-fi.com/epromite/tip) keeps maintenance going.
