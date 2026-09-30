---
name: pvf-a21
description: >-
  Inspect or edit S4A21 DNF PVF archives and scripts. Routes NPC shops,
  CERA cash shops, item definitions, package rewards, and gold drops to their
  domain skills; covers archive output and change records. Read repo AGENTS.md
  for structure and load only the domains needed by the request.
---

# PVF A21 — how to edit

Structure and registries: repo-root [`AGENTS.md`](../../AGENTS.md). Do not load every domain file at once.

Reply to the user in Simplified Chinese.

## Load next

| Task | Read |
| --- | --- |
| NPC shop tabs, listings, categories, `.shp` | [npc-shop/SKILL.md](npc-shop/SKILL.md) |
| CERA cash-shop listings, prices, pages, contracts, catalog migration | [cera-shop/SKILL.md](cera-shop/SKILL.md) |
| Item definitions, NPC item prices, expiry, package rewards, tickets, `.stk`/`.equ` | [items/SKILL.md](items/SKILL.md) |
| Gold drop probability, amount, variance, clear-card multipliers | [gold-drop/SKILL.md](gold-drop/SKILL.md) |

For NPC shop + item edits, update the `.stk`/`.equ` first, then the `.shp` listing. For CERA shop + item edits, load cera-shop and items; close item definitions/rewards before listing in `cerashop.etc`. Listing or pricing alone does not require unrelated domain skills.

For skills, dungeons, monsters, NUT, or other drops without a domain skill, resolve the matching `.lst` where one exists and read the source; follow the hard rules below. Global `etc/` tables do not have a single registry.

## Hard rules

- Default read-only. Without explicit write permission: do not write PVF, do not edit client ImagePacks2/NPK, do not overwrite the baseline pack.
- A numeric ID is not a fact until resolved through the correct `.lst`.
- New items need a free ID, an `.lst` row, and a definition file. Renames do not allocate IDs.
- New blocks / new files: copy 2–3 same-folder, same-extension, same-purpose neighbors. Do not invent tag layout from the tag name.
- Preserve existing tags, backticks, numeric order, and paired `[/...]`; preserve whitespace in editable text. Type 1 binary PVF may normalize rendered whitespace: compare parsed content/order and explain formatting limits. Do not paste skill examples as complete files.
- `.shp` lists items only. Price, bind, effect, expiry: item file.
- `[explain]` is not the effect. Script.pvf resource paths do not prove client assets exist.
- Packed PVF writes require a PVF-aware reader/writer: save a temporary archive, reopen it for validation, then atomically replace only the authorized target. Never edit the archive as text.
- After writes, read the files back. Behavior claims must say how to test in-game. Client and server load the **same** new PVF; restart the server to clear process-level metadata caches. Use reload only when that deployment's reload path is separately verified.
- Before delivering queries or edits, persist newly confirmed, reusable rules in the relevant skill or `AGENTS.md` and its matching Chinese page under [`docs/`](../../docs/README.md) in the same change. Keep essential version/runtime scope; omit source narratives, historical cases, named reference packs, one-off values, and uncertain interpretations. Instructions must work without previously supplied packs. Update both routing indexes for new topics. Do not load `docs/` as edit instructions.

## Project output and change records

- Preserve input and baseline archives. Unless the user specifies another destination, save the validated new PVF in this project's `dist/` as `Script_yyyyMMdd_HHmmss.pvf`, using Beijing time (UTC+8). Check for name collisions and choose a new timestamp; never overwrite an existing output. Save a temporary archive, reopen and verify it, then publish the new filename.
- For actual PVF changes, continue using `dist/改动清单.xlsx`. Preserve existing worksheets; add a worksheet named for the modification function. A1 is `路径`; subsequent rows contain only unique PVF-internal paths with actual content changes or added definitions. No summaries or extra columns. If the exact function name already exists, use a unique function-based suffix and report the chosen name; never replace history.
- Check that the worksheet's path set matches the actual modification set, including changed registries/configuration. Skill/documentation-only changes do not add a PVF change worksheet. Do not update a historical support-analysis workbook as if it were the change log.

## Workflow

```text
read-only close → (after permission) minimal edit → read-back → pack matching client/server PVF → in-game check
```

1. Confirm the system (NPC / item / shop / other) and whether writes are allowed.
2. Resolve name or ID through the matching `.lst`; read the target file. Do not guess from filenames.
3. List paths, fields, old values, new values, and dependents (shops, recipes, quests, packages).
4. Edit only after permission; only planned fields.
5. Read back edited files and related `.lst`.
6. Report: what changed, what did not, pack/load notes, how to verify in-game, whether old item instances refresh.

## Replies

Lead with: can it be done, which files, main risks, next step. Attach IDs, paths, and raw tags when needed.
