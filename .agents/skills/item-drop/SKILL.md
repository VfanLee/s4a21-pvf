---
name: item-drop
description: Inspect or edit A21 monster item pools and quest-item drop quantities/probabilities.
---

# A21 item drops

Use [../SKILL.md](../SKILL.md) for archive authorization/output/validation.
Resolve items, monsters, quests, and dungeons through their respective `.lst`.
Read item definitions first: `[quest]` and `[material]` have different uses.
Distinguish increasing existing yield from adding a new source or removing
quest gating. Do not change item type merely to add a drop.

For same-name monster templates, follow dungeon/dungeon.lst -> target .dgn
`[maze info]` / `[map specification]` map IDs -> map/map.lst -> .map `[monster]`
spawn IDs -> monster/monster.lst -> .mob. Include quest-connected mazes and
alternative map candidates when checking scope. Name search or an editor's
selected same-name result is not evidence of the target dungeon's spawn ID.

Also inspect map `[heroes mode map index]` when covering hero-mode variants;
resolve the referenced map ID through map/map.lst before claiming a runtime
switch. A Hero-prefixed monster/map filename alone does not prove it is active.

For monster rank/title questions, inspect `[monster title]` resource references
separately from `[item]` rewards; dropping a rank-themed item does not establish
rank. Distinguish unique display names from registered templates, and resolve
map spawns before including alternate/event templates in a dungeon edit.

## Generic equipment drops

The current server's generic equipment branch is distinct from `.mob` `[item]`.
It reads `etc/itemdropinfo_monseter.etc` `[drop prob]` in seven-value rows:
level min/max, gold, type1, type2 equipment, type3, uninterpreted final column.
Equipment probability uses the level row's fifth value, category index 2 of
`[monster type drop bonusrate]`, then hardcoded difficulty-index multipliers
`1, 1.2, 1.4, 1.6, 1.8`, with integer truncation and a 10000 cap/denominator.
After a hit, rarity is rolled from row 0 of `[basis of rarity dicision]`;
the grade range from `[item drop ref table]` and weighted equipment pool
determine the item. Empty selected-rarity candidates can fall back to rarity 0;
an empty final pool produces no item. Higher equipment trigger probability
does not by itself raise a specific rarity's selection probability.
Do not apply this branch's difficulty multiplier to `.mob` material pools,
or extend these rules to hell, independent, and clear-card equipment paths.

## Clear-card items

Ordinary settlement cards use `etc/itemdropinfo_clearreward.etc` in the current
server. `[drop prob]` holds named profiles of level-min/max/probability triples.
Free item cards use `default`; paid cards use `event`. Their threshold is
`standardRate/100 * (baseProbability * mapRate + difficultyBonus) * (1-deathPenalty)`,
clamped to 0..10000 and compared to `Next(10000)`. The difficulty bonus is
additive, from `[dungeon difficulty drop bonusrate]`. Free mapRate uses visited
room fraction and `[reward item rate per map max count]`/10000 when present;
paid mapRate is `[gold card create rate]`.

After a hit, `[drop item type prob]` supplies relative type weights; the current
generator supports types 2 (equipment) and 4 (avatar), not types 1/3.
`[basis of rarity dicision]` supplies cumulative thresholds against a 1..1000000
roll, not independent weights. Differences of adjacent thresholds define each
rarity interval; thresholds above 1000000 add no probability. The grade table
and clear-reward equipment pool then select an item; an empty pool yields no
item and has no lower-rarity fallback. Do not equate a rarity interval with
per-card success. `[item drop rarity control]` is parsed but not used by this
generator; `[drop kind prob]` and blank-item tags are not read by this path.

## Quest drops


In this project's ServerS4A21, `.qst` `[monster reward item]` repeats seven
integers: monster ID, dungeon ID, difficulty, item ID, attempt count,
probability percent, held limit. Dungeon/difficulty `-1` means any; monster
ID must match exactly. Only active quests supply candidates.

Each attempt succeeds with `Next(100) < probability`; increasing count adds
attempts, not guaranteed items unless probability is 100. Held quantity is
capped by the smaller applicable value of the row limit and `[seeking]`
`[int data]` required quantity. Raising only the row limit does not raise the
collection goal. Preserve scope and collection targets for yield-only edits.
Test while the quest is active and held quantity is below its effective cap.

## Monster item pools

`.mob` `[item]` repeats item ID / weight pairs, closed by `[/item]`.
The current server first rolls a shared 10% pool trigger, then selects one
item by relative weight and emits one unit. Area material, when enabled and
configured, is merged into this pool with weight 100. Evaluate the merged
pool and dungeon drop policy; a raw weight is not an absolute probability.

Increasing an item's weight only increases its share of the pool. It cannot
raise the shared trigger rate or emitted quantity. A single-entry pool gains
no share from a larger weight unless another source contributes entries.
Higher absolute frequency/quantity requires tracing a supported independent
drop configuration or an explicitly authorized server change. Monster
templates can be shared by multiple dungeons; close map/monster references
before claiming an edit affects only one dungeon.

These runtime meanings apply to the current project server. Verify another
deployment's implementation before promising the same probabilities.

The shared trigger is `MobItemDropRate / DropDenominator` in
`ServerS4A21/Server/DfoServer/Game/Dungeon/DropGenerator.cs`, not a PVF field.
Changing this constant affects every enabled pool using that branch and needs
rebuilding/redeploying the server. For a single item's higher rate, the server
also supports direct-item entries in `etc/independent_drop.etc`; these are
additional rolls, so account for existing pool drops and possible double drops.

## Direct independent drops

Current-server direct rows in `etc/independent_drop.etc` have 17 integers:
`0 monsterId itemId p0 p1 p2 p3 p4 c0 c1 c2 c3 c4 levelMin levelMax difficulty 0`.
Probabilities use denominator 1000000 and difficulty index 0..4. The generator
currently reads only c0 for quantity, even in parties; preserve other columns.
Level bounds are dungeon basis levels and applied only when both are positive;
difficulty -1 permits all difficulties. There is no dungeon-ID column in this
direct format, so a matching monster template can drop in multiple dungeons.
The branch must be enabled by the dungeon drop policy. Bind actual `.mob` IDs
from `monster/monster.lst`, not NPC/APC IDs or names. Human-looking enemies
are not proof of an APC; actual AI-character actors need separate path tracing.
Direct rows are additional item rolls and do not replace `.mob` pools or
require a quest. Verify actual spawn IDs, count and frequency in game.
