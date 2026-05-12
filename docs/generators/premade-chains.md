# Premade tier chains

eGens ships three independent tier chains. Drop any combination
into `plugins/eGens/generators/` and the tier loader picks them up
on next reload. Each chain is self-contained — admins can delete
or replace any chain file without breaking the others, and tier
ids are flat across files so you can cross-promote (`vanilla:diamond`
→ `elemental:fire`) by editing the `next-tier:` pointer.

The bundled balance curve uses a **geometric progression** so each
tier feels meaningfully harder than the previous one:

- Tick speedup: ~9% per tier.
- Capacity scaling: ~1.55× per tier.
- Shop price scaling: ~2.2× per tier.

If those curves don't fit your server's economy, see
[Balance guide](balance-guide.md) — the curve is documented and
the off-tree `tools/gen_chains.py` script in the plugin repo lets
you regenerate the files with different parameters.

## Vanilla chain — `vanilla_chain.yml`

Classic Minecraft progression. **10 tiers**.

| # | Tier | Theme | Block |
|---|---|---|---|
| 1 | `wood` | Hewn from the forest's heart | `OAK_LOG` |
| 2 | `stone` | Quarried from bedrock veins | (stone variant) |
| 3 | `copper` | Oxidising slowly under the sky | (copper variant) |
| 4 | `iron` | The workhorse of every server | (iron variant) |
| 5 | `gold` | Bright and brittle | (gold variant) |
| 6 | `redstone` | Pulsing with raw current | (redstone variant) |
| 7 | `lapis` | Deep-blue lustre | (lapis variant) |
| 8 | `diamond` | Crystalline payday | (diamond variant) |
| 9 | `emerald` | Mountain-rare riches | (emerald variant) |
| 10 | `netherite` | End-of-line apex | (netherite variant) |

Chain: `wood → stone → copper → iron → gold → redstone → lapis →
diamond → emerald → netherite`.

## Mob chain — `mob_chain.yml`

Hostile-mob trophies, escalating in danger. **10 tiers**.

| # | Tier | Theme |
|---|---|---|
| 1 | `zombie` | Shambling staple |
| 2 | `skeleton` | Bone-rattler |
| 3 | `spider` | Silk and venom |
| 4 | `creeper` | Hissing surprise |
| 5 | `enderman` | Tall and teleporting |
| 6 | `blaze` | Smouldering rod |
| 7 | `ghast` | Crying fireball lobber |
| 8 | `witch` | Brewer of woes |
| 9 | `shulker` | Floating cube of regret |
| 10 | `wither` | Three-headed cap |

Chain: `zombie → skeleton → spider → creeper → enderman → blaze →
ghast → witch → shulker → wither`.

## Elemental chain — `elemental_chain.yml`

Classical elements + derivatives. **8 tiers**.

| # | Tier | Theme |
|---|---|---|
| 1 | `earth` | Solid foundation |
| 2 | `water` | Flowing wealth |
| 3 | `fire` | Volatile sparks |
| 4 | `air` | Breeze of fortune |
| 5 | `ice` | Frozen accumulation |
| 6 | `lightning` | Crackling rush |
| 7 | `ender` | Through the void |
| 8 | `void` | Beyond all elements |

Chain: `earth → water → fire → air → ice → lightning → ender →
void`.

## Replacing or extending

- **Delete a chain** — remove the file. The tier ids in it become
  unknown; any in-world generators of those tiers will fail to
  resolve and stay paused until the file (or replacement
  definitions) returns. Save your players the heartburn — only
  delete a chain when no live generators reference it.
- **Add a new chain** — drop another `.yml` file (any name other
  than `drops.yml`) in `generators/`. The loader scans every
  non-`drops.yml` file under that folder.
- **Cross-promote** — set `next-tier:` to a tier from another
  chain. Tier ids are flat; only uniqueness matters.

## Versioning

The bundled chain files do not carry a `config-version` field —
chains are user content. eGens will load whatever schema-valid
chains it finds, including third-party content packs.

---

[Tip on Ko-fi](https://ko-fi.com/epromite/tip) keeps maintenance going.
