# Storage

eGens persists generator state, audit logs, sellwand uses, personal
events, player settings, and idempotency markers in a relational
database. Two backends are supported:

| Backend | When to use it |
|---|---|
| **SQLite** (default) | Single-instance servers, small networks, dev/test. Zero setup — a file is created under `plugins/eGens/storage/`. |
| **MySQL / MariaDB** | Multi-instance networks, BungeeCord/Velocity setups where multiple game servers share inventory data, larger servers where you already have a managed DB. |

Switch via `storage.type` in `config.yml`:

```yaml
storage:
  type: SQLITE    # SQLITE | MYSQL | MARIADB
```

## SQLite

```yaml
storage:
  type: SQLITE
  sqlite:
    file: "storage/egens.db"
```

- The file path is relative to `plugins/eGens/`.
- WAL mode is enabled automatically — that's why you'll see
  `egens.db-wal` and `egens.db-shm` next to the main file. Don't
  delete them while the server is running.
- Backups are atomic: `/egens backup` runs a `VACUUM INTO` snapshot
  while the live connection stays writable. See
  [Backups](../operations/backup.md).

## MySQL / MariaDB

```yaml
storage:
  type: MYSQL
  mysql:
    host: db.example.internal
    port: 3306
    database: egens
    username: egens
    password: hunter2
    pool-size: 10
    connection-timeout-ms: 5000
```

Recommended setup:

1. Create the database and a dedicated user:

   ```sql
   CREATE DATABASE egens CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
   CREATE USER 'egens'@'%' IDENTIFIED BY 'hunter2';
   GRANT ALL PRIVILEGES ON egens.* TO 'egens'@'%';
   FLUSH PRIVILEGES;
   ```

2. Make sure the MySQL user can also create indexes and run DDL.
   eGens runs schema migrations on every boot and creates new
   tables as features ship. Lack of DDL permission will surface as
   a startup error in console.

3. Use `utf8mb4` collation. The plugin stores MiniMessage strings,
   audit JSON, and player display names; the collation must handle
   the full Unicode range.

4. Backups use `mysqldump --single-transaction` and gzip the output
   into `plugins/eGens/backups/egens-YYYYMMDD-HHmmss.sql.gz`. The
   `mysqldump` binary must be on the same machine's `PATH`. If it
   isn't, you'll see a single warning on boot and `/egens backup`
   will return a `Failure` result with a clear message.

## Multi-instance servers

If multiple game servers point at the same MySQL database:

- They will all share generator state, player settings, sellwand
  uses, audit logs.
- The `anti-dupe.use-row-lock: true` switch in `config.yml` enables
  row-level locking for transactional updates — required for
  multi-instance to be safe. Don't disable it on MySQL.
- Each server still has its own `plugins/eGens/` folder; only the
  database is shared. Keep `config.yml`, `events.yml`, the
  `generators/*.yml` chains, etc. in sync between servers (e.g. via
  a network filesystem or your deployment tool).

## Migrations

eGens runs schema migrations automatically on boot. The current
schema version is recorded in `egens_schema_version`. If you ever
need to inspect or audit migrations:

```sql
SELECT version, script, applied_at FROM egens_schema_version ORDER BY version;
```

Never delete rows from `egens_schema_version` — the plugin uses it
to decide which migrations to skip. Roll forward only; downgrades
require restoring a DB backup taken before the upgrade.

## Troubleshooting

- **"Can't connect to MySQL"** — check host/port, firewall rules,
  and that the MySQL user can connect from the server's IP. The
  error always lists the failing JDBC URL.
- **"Pool exhausted"** — raise `mysql.pool-size`. The default of 10
  is fine for ~200 concurrent players; busier servers can go to
  20–30.
- **"Migration X failed"** — surface the full stack trace from
  console and report it. Don't keep rebooting; the migration runner
  is transactional and won't partial-write, but a persistent
  failure means the schema is incompatible and needs attention.

---

[Tip on Ko-fi](https://ko-fi.com/epromite/tip) keeps maintenance going.
