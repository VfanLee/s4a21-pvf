---
name: dungeon-difficulty
description: >-
  Inspect, compare, or adjust S4A21 次元彼端 dungeon difficulty: global and independent
  monster/APC tables, .dgn difficulty fields, and registered dungeon entrances.
  Use for ordinary/Otherverse/ancient/Soul dungeon difficulty, monsterapcdifficultybonus.tbl, monsterapc diff table,
  difficulty comparisons, or checking whether legacy dungeons have entrances.
  Gold and item drop rates belong to gold-drop and item-drop.
---

# A21 dungeon difficulty

Scope: S4A21 次元彼端. Apply [../SKILL.md](../SKILL.md) for designated
targets, no-target answers, conflicts and read-only reference projects.

## Edit boundary and read-back

Resolve dungeon IDs through `dungeon.lst`, independent-table paths from `.dgn`,
and entrance links through town/worldmap/dungeon/map registries or explicit paths.
Group all consumers of shared tables/regions before editing. Change only verified
attributes/columns or agreed entrance references; unknown blocks keep their values
and are not assigned guessed labels. Reopen, compare numeric types/order and
close references, then verify combat or entrance behavior separately in-game.

Read [../SKILL.md](../SKILL.md) for archive writes, validation, and change records.
These rules apply to the documented S4A21 次元彼端 runtime; do not import another version's
block labels, difficulty names, or line numbers.

## Find the effective configuration

- `etc/ultimatedungeonlist.etc [apply ultimate]` is a flat dungeon-ID list;
  resolve each integer through `dungeon/dungeon.lst`, not as ID/value pairs.
  Reference ServerS4A21 C# sources have no loader reference to this file/tag.
  Membership alone does not establish difficulty unlocks, stat scaling, drop
  bonuses, or the original meaning of "ultimate"; verify the actual consumer.
- `dungeon/dungeon.lst` maps dungeon ID to a relative `.dgn` path; it is not the
  dungeon's configuration. Resolve the registered path, then read `[name]` and
  the definition. Distinguish same-name normal, quest, event, and legacy variants.
- `monster/monsterapcdifficultybonus.tbl` is the global default difficulty table.
- A `.dgn` `[monsterapc diff table]` explicitly references a separate table.
  Normalize separators, compare archive paths without case sensitivity, and
  verify that the referenced file exists. Group registered dungeons by table
  path before edits: one table can serve several dungeons.
- Inspect `[monster difficulty bonus]`, `[difficulty]`, `[difficulty level]`,
  and `[designate dungeon difficulty]` separately when present. A table
  reference does not establish how global and local bonuses combine or which
  difficulty a client selects; confirm the target consumer before claiming it.

An independent table is an explicit reference, not necessarily a table unique
to one dungeon. Preserve unrelated difficulty fields during multiplier edits.

## Otherverse and ancient-dungeon lookup

These are the documented A21 family-specific difficulty-table paths. Verify
the active registered `.dgn` references in each supplied target:

| Family | Independent difficulty table |
| --- | --- |
| Otherverse, including merged dimensional-rift definitions | `dungeon/impossible/table/monsterapcdifficultybonus.tbl` |
| Ordinary ancient dungeons | `dungeon/ancient/table/monsterapcdifficultybonus.tbl` |
| Soul ancient dungeons (Requiem mode) | `dungeon/ancient/table/r_monsterapcdifficultybonus.tbl` |

Ordinary ancient and Requiem use separate table files. Adjust the referenced
ordinary table for ordinary ancient combat and the referenced `r_` table for
Requiem combat; changing one does not edit the other. The two mode labels do
not represent two columns within a single table. This file-family mapping does
not establish the meanings of stat groups or the effective difficulty column.

Close town/worldmap selection to the exact dungeon IDs before naming the affected
version. Similar ancient filenames can lack a table reference; do not assume
every `.dgn` under `ancient/` uses its ordinary ancient table. Otherverse solo
tutorials and ancient quest variants can share the corresponding table; group
all registered consumers and isolate them when the requested scope excludes
them. Inspect Otherverse `.dgn` `[monster difficulty bonus]` alongside the
table without assuming a composition formula. Attribute-column meanings remain
subject to consumer verification; never multiply the whole table to increase
combat difficulty or change difficulty-selection flags as a substitute for
verified stat multipliers.
Ancient/Soul selection can use separate registered dungeon IDs and XUI buttons
while both definitions retain numerical difficulty fields. A mode label such
as ordinary ancient does not establish difficulty index 0, nor does Soul imply
the next table column. Close each XUI `dungeonIndex` to its `.dgn` and table,
read designated difficulty separately, and verify the actual client/request
index before naming the effective column. Parsed designation is not proof of
client table indexing or combat behavior.

## Monster levels and Mirror Arad

Normalize `.lst` relative path separators before archive lookups. Close Mirror
Arad via registered worldmap `[dungeon]` -> wrapper `.dgn`, and
`etc/crackofdimensionlist.etc [crack info list]` dungeon/quest pairs -> actual
historical `.dgn` and registered quest. Inspect `[mob level charac level replace
flag]`, `[basis level]`, `[crack of dimension dungeon]`, `[revision table]`, and
map monster level fields separately. Reference ServerS4A21 parses the character
level replacement flag but has no consumer reference to that property; do not
promise values 0/1/2 implement a verified scaling policy. Its ordinary actor
projector uses map Lv as the switch: nonzero -> dungeon basis level + AutoLv,
zero -> absolute AutoLv; nonpositive results fall back to dungeon basis level.
Quest/dungeon pairing and level-based mission rewards do not prove monster
levels follow quest level. Verify the actual deployment before claiming
character-level or quest-level scaling or revision-table formulas.

## Blood-altar monster levels (reference ServerS4A21)

`.dgn` `[champion]` is an indexed base parameter for ordinary champion
promotion, not monster level or HP/attack multipliers. Reference
`Dungeon.GetChampionCount` selects by difficulty index, applies integer
multipliers 1.5/2.5/5 for indices 1/2/3 (otherwise unchanged), then compares
`100 * adjusted / (mazeWidth * mazeHeight)` to `Next(100)` and returns 0 or 1.
Do not describe raw entries as direct per-monster percentages. The dedicated
blood-altar wave generator assigns normal/boss types itself and does not use
this parameter for its scheduled waves; increasing it is not a verified altar
difficulty adjustment.

Endless blood-altar schedules use difficulty 0 and do not randomly select the
five `[champion]` columns. Ultimate altar is separate: its schedule can use
phase difficulty 1/2; the coordinator's selection timeout randomly resolves
1 or 2. Do not transfer that timeout behavior to endless altar entry.

For registered `[blood dungeon]` modes, close `.dgn` -> map registry ->
`.map` `[blood monster]` / `[blood phase time]` (endless) or ultimate sections.
The blood-altar definition copies `.dgn` `[basis level]`, and scheduled waves
use that value directly for every spawned actor's level; ordinary MAP Lv/AutoLv
rules are not this branch. Raise the designated DGN basis level for a fixed
level change; keep entry minimum, rounds, waves and rewards unchanged unless
separately requested. The loader requires basis level 1..255. Level projection
does not verify combat-stat multipliers or `[revision table]` effects; test
actual client combat separately rather than claiming a specific HP increase.

## Story-mode configuration

In the documented S4A21 次元彼端 runtime, story dungeons in **simple mode**
use the global difficulty table with the same difficulty behavior as ordinary
dungeons; the global white-dungeon block also affects this mode. Do not extend
this rule to other story difficulty modes or assume an unverified column index
from the word "simple". Still check any explicit independent-table reference.

For other story difficulty modes, inspect `monster/storymodedifficultybonus.tbl` as well as
the global table and any explicit independent-table reference. Identify the
mode from the registered `.dgn` `[story mode]`, not from a quest-like name or
folder alone. This block can contain `[difficulty size]`, `[first difficulty
rate]`, `[increase difficulty rate]`, `[increase exp rate]`, and `[quest list]`.
The reference ServerS4A21 dungeon parser preserves these arrays and quest links; parsing
does not prove their combat scaling formula. Outside the confirmed simple mode,
do not promise that editing the white-dungeon block changes story combat, or
that the story table replaces or multiplies the global table, without consumer
or in-game verification. Treat
experience-rate fields separately from monster combat difficulty.

## Global table layout

The untagged prefix has four blocks in this project's inspected A21 layout:

| Block | Numeric positions, one-based | Attribute groups | Values per group | Scope |
| --- | --- | --- | --- | --- |
| 1 | 1–65 | 13 | 5 | Ordinary dungeons |
| 2 | 66–130 | 13 | 5 | Unconfirmed |
| 3 | 131–182 | 13 | 4 | Unconfirmed |
| 4 | 183–234 | 13 | 4 | Unconfirmed |

Positions count numeric values in the untagged prefix, excluding headers and
comments. Confirm this shape in the actual target before positional edits.
Editor wrapping is not a schema: five values per displayed line splits the
four-value groups. Reformat only for inspection; preserve token order and types.

For a white-dungeon-only adjustment, check independent-table references first,
then scope the global edit to block 1. Do not call block 2 a tower block, or
assign monster/APC, normal/named/boss, or party labels to blocks 2–4.
Attribute and difficulty-column meanings require their own confirmation; the
known white-dungeon scope does not verify every attribute or column label.

Named sections such as `[dungeon party balance]`, `[normal]`, `[expert]`,
`[master]`, `[king]`, `[slayer]`, and `[adjust hp gauge]` are separate structures.
Do not treat them as more rows of the prefix or infer actual HP solely from
displayed HP-bar counts. Independent tables must be inspected in full rather
than assumed to have the same prefix length.

## Read and compare accurately

- Use a PVF-aware reader. Reference `PvfLib.PvfArchive` supports
  `GetFileContent` and `GetFileRawData`; confirm its actual API before use.
  `TableFile.Parse` retains only `long.TryParse` values and cannot fully read
  mixed float/tag tables. Type 1 integer tokens are type 0, floats type 2.
- Compare parsed tags, strings, numeric values, order, and numeric types;
  whitespace or relocated archive string offsets alone are not gameplay edits.
  Align sections/groups before reporting positions if a sequence was inserted
  or deleted; an index shift is not a change to every subsequent value.
- Use the user-designated baseline. Compare files by normalized internal path
  in both archives and record additions/removals separately. For an archive-wide
  difficulty audit, include unreferenced difficulty tables and all archived
  `.dgn` difficulty fields; identify registered consumers separately.
- Compare registry mappings independently. A newly registered ID can refer to
  an unchanged `.dgn` already in the baseline. Missing registration is not a
  missing definition or a newly added difficulty field.
- Report table/section/group/column, old and new values, and affected registered
  dungeon IDs/names/paths. Do not claim that difficulty is unchanged overall if
  the audit excludes individual `.mob` properties, base-parameter tables, skills,
  or scripted mechanics.

For a confirmed multiplier, use `new = old × requested ratio`; setting the
multiplier to the ratio itself is different. Additive rates, probabilities,
resistances, speed values, and thresholds need their own semantics. Never scale
all numeric tokens as if every field were an HP multiplier.

## Entrance availability and verification

Existing town areas can host ordinary selection entrances: MAP `[town movable
area]` rows ending `-1 -1` open the current area's dungeon selection; the TOWN
area must have `[dungeon gate]` pointing to a registered worldmap region.
Gate animations are visual resources, not the destination binding. Trace the
area's exact MAP path and any deployment-selected variants before changing it.
When adopting an entrance animation, close MAP -> relative ANI -> IMAGE path
and frame references. The ANI must exist in the target PVF even when its image
already exists in the client's NPK; copy only missing required definitions.
A configured direct town entrance does not require a new intermediary area.
For an entrance-only probe, reuse a registered ordinary region with unchanged
WDM, XUI, dungeon registry and dungeon/MAP definitions; validate the town
return projection and existing start/Boss room loading separately. Each TOWN
area has one dungeon-gate region binding, so multiple local `-1 -1` triggers
within it share that destination. Distinct destinations need distinct bound
areas or another separately verified routing mechanism.
Replacing a registered region's WDM dungeon list and referenced XUI changes
the selection for every town gate bound to that region. For a probe using an
already working entrance, preserve the town binding and physical trigger,
align WDM dungeon/quest entries with XUI dungeonIndex values, and verify
ordinary admission and ticket conditions separately. Keep the original dungeon
definitions and a rollback archive; shared-region replacement is not an
entrance-specific destination change.
For a visible but inactive town link, correlate real player foot coordinates
with the trigger and walkable rectangles, then inspect whether the client
reports SET_USER_AREA to the intended area or ENTER_SELECT_DUNGEON while still
in the source area. Parsed destination/return-point validity does not prove a
client transition. Treat walk-boundary trigger changes as probes until tested.
The reference ServerS4A21 town-return projector reads an area's `MapPath`
directly under `map/`; that lookup does not use `map/map.lst`. Do not confuse
this path-based town MAP lookup with registered dungeon-room MAP IDs. A
same-directory town MAP clone can preserve raw payload and relative resources
when only the TOWN area binding changes; check its back-link and return point.
After a client crash, restore only the known changed deployment files from a
hash-verified pre-change backup, retain the failing package, and distinguish
file restoration from process restart. Do not infer the crash cause from a
successful server-only parser test.
When a selection crash is reported, verify the hashes of both deployed
archives and retain the corresponding runtime logs before another probe.
An observed ENTER_SELECT_DUNGEON request followed by a successfully sent
response establishes that the trigger reached the server; it does not prove
that the client loaded WDM/XUI or identify the crash cause. Preserve XUI control
order when editing buttons, and mark failed client tests separately from
successful static validation. If automatic deployment was excluded, provide
the verified rollback archive without replacing running files.

For independent legacy clones in reference ServerS4A21, give copied MAPs a
separate directory as well as a new `[dungeon]` owner and registry ID; ordinary
room pools also use directories. Rewrite relative resource references from
the new directory to existing assets. Use explicit `[map specification]`
entries to avoid filename-coordinate fallbacks. Check every nonempty maze
cell against the selected MAP's entrance mask, reciprocal neighboring doors,
start-room actors, Boss actors and town return projection. A successful archive
reload or generic parser self-test alone does not verify connected rooms.
The A..P mask is letter minus A: right 1, above 2, left 4, below 8;
MAP greed uses the second two-character symbol in each pair as entrance mask.
Client selection controls, minimap labels and actual play still need in-game
verification; registry closure does not prove world-map presentation.

Reference ServerS4A21 loads ordinary dungeon and worldmap definitions from PVF
registries; reusing supported ordinary layouts can add selectable dungeons
without changing server source. Close town area `[dungeon gate]` region ID,
`worldmap.lst` registration, WDM `[dungeon]` ID/quest pairs, and `dungeon.lst`
definitions. Client WDM `[ui path]` and image assets must support the selection
layout; server data loading does not create missing client UI or new mechanics.
Inspect reused legacy definitions for entry items, required level, basis level
and special-mode flags before presenting them as ordinary low-level dungeons.

For an A21 manual-abyss availability list, close active town gates and
registered worldmap regions before listing registered dungeons. Require region
`[hell dungeon] 1`, its registered `[hell quest]` prerequisites, and a usable
dungeon-maze hell room. Inspect `[seal door map index]` / `[seal door pos]`,
resolve the map through `map/map.lst`, and confirm `[hellparty]` content;
coordinate-named `hell_` maps are a separate runtime fallback. A region flag
alone, or a dungeon `[hell dungeon]` value alone, does not prove availability.
Parse WDM dungeon entries as dungeon ID, optional `[in progress]`, then quest
ID across whitespace; identify quest-only variants separately. Reference
ServerS4A21 requires every positive region hell-quest ID to be completed, even when
that ID is absent from `quest.lst`; report dangling gates rather than calling
those regions unlocked. Saved completion state can differ by character.

Separate unlock-quest acceptance level from each dungeon's entry level.
Present static findings as a configured unlock path, not a completed in-game
verification. Regions with a hell flag but no verified hell room, including
special/event entrances, must be listed separately from ordinary manual abyss.

Keep three findings separate: an archived definition, a registered dungeon,
and a configured entrance. Trace registered town `.twn` `[dungeon gate]` region
ID → `worldmap/worldmap.lst` → `.wdm` `[dungeon]` references and their conditions.
An unregistered archived `.wdm` does not prove an active region; check custom
regions because new entrances can reuse old dungeon IDs. Static closure does
not prove the player's access conditions, client resources, or server support.

For an authorized change, follow the shared packed-write workflow. Reopen and
compare the output so only planned paths/groups/columns change. Client and
server must load the same output; restart the server. In-game, hold dungeon,
difficulty, party size, character, and equipment constant and test the intended
effect. A change to one confirmed field is a useful probe when its consumer is
unclear; do not claim that a probe has already verified runtime behavior.
