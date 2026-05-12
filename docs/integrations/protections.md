# Region protections

eGens consults a region-protection plugin before generators are
**placed** and before generators are **broken** — even by the
generator's owner, when the configured provider says the player
isn't allowed to interact with that block. Claim membership
trumps generator ownership.

Four adapters ship:

| Provider | Plugin |
|---|---|
| `WORLDGUARD` | [WorldGuard](https://dev.bukkit.org/projects/worldguard) |
| `GRIEFPREVENTION` | [GriefPrevention](https://www.spigotmc.org/resources/griefprevention.1884/) |
| `LANDS` | [Lands](https://www.spigotmc.org/resources/lands.53313/) |
| `TOWNY` | [Towny](https://www.spigotmc.org/resources/towny-advanced.72694/) |

## Configuration

`config.yml`:

```yaml
integrations:
  protection: AUTO          # AUTO | NONE | WORLDGUARD | GRIEFPREVENTION | LANDS | TOWNY
```

- `AUTO` probes the supported plugins in order
  (`WorldGuard → GriefPrevention → Lands → Towny`) and picks the
  first one that's enabled. Falls back to `NONE` if none are
  loaded.
- `NONE` disables the integration entirely — generators can be
  placed in any claim regardless of build permissions.
- The named providers pin eGens to that specific plugin even when
  the others are also installed.

## Per-provider rules

### WorldGuard

eGens checks the **BUILD** flag at the placement location and at
the location of every existing generator before allowing a break.
Players without `BUILD` (or whose region denies it) can't place /
break eGens generators there.

### GriefPrevention

eGens calls GriefPrevention's `allowBuild()` / `allowBreak()` for
each operation. Players who don't have build/break access in the
claim are denied.

### Lands

eGens checks the `BLOCK_PLACE` / `BLOCK_BREAK` flags at the
target location. Land members and trustees with those flags can
build; non-members can't.

### Towny

eGens calls Towny's BUILD / DESTROY action permission. Plot
owners, residents with the right permission node, and town
officials with build perms can place / break.

## Override permission

`egens.world.bypass` skips the protection check entirely (it also
skips the world whitelist/blacklist). Use sparingly — typically
only for staff accounts that need to clean up rogue generators.

## Skyblock integrations

There is no built-in adapter for BentoBox / IridiumSkyblock /
ASkyBlock / SuperiorSkyblock. The user-facing decision was to
keep the protection adapter surface focused on the big four named
above. Skyblock owners typically either:

- Use one of the big-four adapters (e.g. Lands often runs
  alongside BentoBox).
- Disable eGens protections (`integrations.protection: NONE`) and
  rely on the skyblock plugin's island-only build permission to
  enforce placement.

## Hot-reload

`/egens reload` re-reads `integrations.protection` and switches
adapters without a restart. Existing generators continue to tick;
only the next placement / break check uses the new adapter.

## Troubleshooting

- **"This area is protected" when there shouldn't be one** —
  verify which adapter was picked. The startup banner logs the
  effective protection provider. Switch to `NONE` temporarily to
  confirm whether the protection plugin or eGens is denying the
  action.
- **Claim member with build perms still can't break** — confirm
  whether the protection plugin distinguishes "build" from
  "break" / "destroy". GriefPrevention does; WorldGuard's BUILD
  flag covers both. eGens calls the right method per adapter, but
  some bespoke setups disable one and not the other.

---

[Tip on Ko-fi](https://ko-fi.com/epromite/tip) keeps maintenance going.
