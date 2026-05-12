# Backups

eGens ships a built-in database snapshot service so you can roll
back from an exploit, a bad migration, or a catastrophic player
mistake without needing a separate backup tool.

## Master switches

`config.yml`:

```yaml
backup:
  auto-backup: true           # periodic timer master switch
  interval-hours: 6           # 0 disables periodic
  retention-count: 7          # keep N most-recent; 0 disables pruning
  directory: "backups"        # relative paths resolve under plugins/eGens/
```

- The periodic timer runs on a server-side scheduler. Each fire
  invokes the same code path as `/egens backup`.
- Manual snapshots via `/egens backup` work regardless of
  `auto-backup`.

## Where snapshots live

| Backend | File format |
|---|---|
| SQLite | `plugins/eGens/backups/egens-YYYYMMDD-HHmmss.db` (atomic snapshot via `VACUUM INTO`, falls back to a WAL-checkpointed file copy on older builds). |
| MySQL / MariaDB | `plugins/eGens/backups/egens-YYYYMMDD-HHmmss.sql.gz` (output of `mysqldump --single-transaction` gzipped). |

Filenames sort lexicographically — newest at the bottom of a
listing. The retention prune deletes the lowest-sorted (oldest)
files until `retention-count` are left.

## MySQL prerequisites

The `mysqldump` binary must be on the server host's `PATH`. eGens
checks at boot and logs a single warning if it's missing:

```
[eGens] mysqldump not found on PATH — manual /egens backup will fail.
```

Periodic snapshots are still scheduled, but they'll record a
`BackupResult.Failure` with a clear message until you install
`mysqldump`. On Debian/Ubuntu: `sudo apt install mysql-client-core`.
On RHEL/Fedora: `sudo dnf install mysql`.

## Triggering a manual snapshot

```
/egens backup
```

Permission: `egens.admin.backup`. Console-safe.

The command returns one of:

- `command.backup.success` — snapshot written; the response
  includes the resulting filename.
- `command.backup.failure` — snapshot failed; the response
  includes the error message tail.

## Restoring

Restore is intentionally **manual** — automating it on a live
server is too risky. The recipe:

### SQLite

```
# 1. Stop the server.
# 2. Move the live db out of the way.
mv plugins/eGens/storage/egens.db plugins/eGens/storage/egens.db.broken
# 3. Copy the snapshot over.
cp plugins/eGens/backups/egens-20260512-013300.db plugins/eGens/storage/egens.db
# 4. Start the server.
```

### MySQL

```
# 1. Stop the server.
# 2. Drop the target database (or restore into a fresh one).
mysql -u root -p -e "DROP DATABASE egens; CREATE DATABASE egens CHARACTER SET utf8mb4;"
# 3. Pipe the snapshot back in.
gunzip < plugins/eGens/backups/egens-20260512-013300.sql.gz | mysql -u root -p egens
# 4. Start the server.
```

eGens runs migrations on the next boot, which is harmless against
a snapshot taken from the same major schema version.

## Snapshot atomicity

- SQLite `VACUUM INTO` produces a fully-consistent snapshot
  without blocking writers.
- `mysqldump --single-transaction` creates a transactional read
  view so the snapshot is internally consistent across all tables.

You **can** safely run snapshots during heavy load, but the dump
process holds open connections and uses disk I/O, so schedule
periodic snapshots during quieter hours when possible (the bundled
default of 6 hours roughly aligns with most server day-cycles).

## Retention

`backup.retention-count: 7` keeps the seven newest snapshots and
prunes the rest after each successful run. The pruner is
defensive — it never deletes files that don't match the
`egens-YYYYMMDD-HHmmss.<ext>` naming pattern, so any other files
you drop into the backup directory are left alone.

## Off-server backup

`backup.directory` accepts absolute paths. Point it at a mounted
network volume (NFS, SMB, EFS, …) and your snapshots live off the
game server's local disk. Example:

```yaml
backup:
  directory: /mnt/backups/egens
```

For maximum durability, pair this with a daily rsync to cold
storage (S3 / B2 / your provider of choice) — eGens doesn't do
that for you, but the filename pattern makes scripting trivial.

## Audit log

The audit log (`anti-dupe.audit-log: true`) is captured *inside*
the database snapshot, so a restore brings the audit history
along with the state. Don't truncate the audit table without also
taking a fresh snapshot.

---

[Tip on Ko-fi](https://ko-fi.com/epromite/tip) keeps maintenance going.
