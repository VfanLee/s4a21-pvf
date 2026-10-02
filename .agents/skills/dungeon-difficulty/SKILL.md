---
name: dungeon-difficulty
description: >-
  Inspect, compare, or adjust A21 dungeon difficulty: global and independent
  monster/APC tables, .dgn difficulty fields, and registered dungeon entrances.
  Use for 白图难度, monsterapcdifficultybonus.tbl, monsterapc diff table,
  difficulty comparisons, or checking whether legacy dungeons have entrances.
  Gold and item drop rates belong to gold-drop and item-drop.
---

# A21 dungeon difficulty

Read [../SKILL.md](../SKILL.md) for archive writes, validation, and change records.
These rules apply to this project's A21 runtime; do not import another version's
block labels, difficulty names, or line numbers.

## Find the effective configuration

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

## Monster levels and Mirror Arad

Normalize `.lst` relative path separators before archive lookups. Close Mirror
Arad via registered worldmap `[dungeon]` -> wrapper `.dgn`, and
`etc/crackofdimensionlist.etc [crack info list]` dungeon/quest pairs -> actual
historical `.dgn` and registered quest. Inspect `[mob level charac level replace
flag]`, `[basis level]`, `[crack of dimension dungeon]`, `[revision table]`, and
map monster level fields separately. Current ServerS4A21 parses the character
level replacement flag but has no consumer reference to that property; do not
promise values 0/1/2 implement a verified scaling policy. Its ordinary actor
projector uses map Lv as the switch: nonzero -> dungeon basis level + AutoLv,
zero -> absolute AutoLv; nonpositive results fall back to dungeon basis level.
Quest/dungeon pairing and level-based mission rewards do not prove monster
levels follow quest level. Verify the actual deployment before claiming
character-level or quest-level scaling or revision-table formulas.

## Story-mode configuration

In this project's A21 runtime, story dungeons in **simple mode (简单模式)**
use the global difficulty table with the same difficulty behavior as ordinary
dungeons; the global white-dungeon block also affects this mode. Do not extend
this rule to other story difficulty modes or assume an unverified column index
from the word "simple". Still check any explicit independent-table reference.

For other story difficulty modes, inspect `monster/storymodedifficultybonus.tbl` as well as
the global table and any explicit independent-table reference. Identify the
mode from the registered `.dgn` `[story mode]`, not from a quest-like name or
folder alone. This block can contain `[difficulty size]`, `[first difficulty
rate]`, `[increase difficulty rate]`, `[increase exp rate]`, and `[quest list]`.
The current dungeon parser preserves these arrays and quest links; parsing
does not prove their combat scaling formula. Outside the confirmed simple mode,
do not promise that editing the white-dungeon block changes story combat, or
that the story table replaces or multiplies the global table, without consumer
or in-game verification. Treat
experience-rate fields separately from monster combat difficulty.

## Global table layout

The untagged prefix has four blocks in this project's inspected A21 layout:

| Block | Numeric positions, one-based | Attribute groups | Values per group | Scope |
| --- | --- | --- | --- | --- |
| 1 | 1–65 | 13 | 5 | Ordinary dungeons (白图) |
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

- Use a PVF-aware reader. Existing local `PvfLib.PvfArchive` supports
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

For an A21 manual-abyss availability list, close active town gates and
registered worldmap regions before listing registered dungeons. Require region
`[hell dungeon] 1`, its registered `[hell quest]` prerequisites, and a usable
dungeon-maze hell room. Inspect `[seal door map index]` / `[seal door pos]`,
resolve the map through `map/map.lst`, and confirm `[hellparty]` content;
coordinate-named `hell_` maps are a separate runtime fallback. A region flag
alone, or a dungeon `[hell dungeon]` value alone, does not prove availability.
Parse WDM dungeon entries as dungeon ID, optional `[in progress]`, then quest
ID across whitespace; identify quest-only variants separately. The current
server requires every positive region hell-quest ID to be completed, even when
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
