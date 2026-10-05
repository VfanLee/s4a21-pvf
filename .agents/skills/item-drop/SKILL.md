---
name: item-drop
description: Inspect or edit S4A21 次元彼端 global material drops, monster item pools, and quest-item drop quantities/probabilities.
---

# A21 item drops

Scope: S4A21 次元彼端. Use [../SKILL.md](../SKILL.md) for the designated
target, no-target answers, conflict handling and read-only reference boundary.

## Edit boundary and read-back

Close the dungeon → map → actor and item/quest registry chains for the actual
source. Select one concrete generation branch before interpreting probabilities,
weights or quantities; the sections below give their different units/grouping.
Change only requested rows/columns and preserve other sources, quest conditions
and item types. Shared templates/tables can affect several dungeons.
Reopen and compare group alignment, candidate IDs, rates/counts and unchanged
records. Test the intended policy, actor, difficulty and quest/held-count state;
do not equate configured probability with a measured per-run rate.

For external independent-drop bookmarks, inspect nonempty content and the
loader before following `etc/independentdrop.lst` or its child `.etc` files.
Retained empty files do not establish an active external drop chain.
`etc/itemdictionary/(r)independentdropinfo.etc` has `[item table]` / `[list]`
display data; it is not a substitute for the actual drop-generation source.

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

## Global material drops

`etc/worlddrop.etc` `[world drop]` contains monster-level groups: level,
one reserved integer, item-ID/weight pairs, then the standalone `-1` terminator.
Resolve target items in `stackable.lst`; inspect every group, not just the first
text match. To disable only a target's global source, set its weight to `0` in
all matching pairs, preserving IDs, group headers, other pairs, and terminators.
Do not remove only an ID, replace it with gold ID `0`, or edit pickup membership
as a substitute for disabling drops. Removing a complete pair must preserve
the parser's pair alignment.

Count matches as parsed item-ID/weight pairs and report their monster-level
groups, not raw numeric text hits. A level group can exist without the target
item; absence of that pair is not absence of the level group. Check both the
group's membership and weight before explaining excluded levels. Line wrapping
does not delimit groups; the standalone terminator does.

Reference ServerS4A21 `WorldDropSystem` ignores item IDs <= 0 and weights <= 0.
It uses monster level, supports generation at levels 1–199, and caches the
table until process restart. Positive weights sum to W; trigger threshold is
W against `Next(100000)`, then one item is selected by relative weight and
one unit emitted. Total weight affects the shared trigger, not just selection;
do not redistribute disabled weights to other items without a separate request.
This disables only world-drop entries, not `.mob` pools, area materials,
independent drops, or quest rewards. Verify those separately if all sources
must be removed. Read back every target weight and compare unrelated entries.

## Generic equipment drops

In reference ServerS4A21, `MonsterDropConfig` loads only
`etc/itemdropinfo_monseter.etc`; no C# loader reference to
`etc/itemdropinfo_monseter_extra.etc` is present. Do not promise additive drops,
elite-only drops, or extra-file overrides from its filename. Other server
implementations need their own loader/caller verification.

This file also drives generic gold and stackable branches: `[drop prob]` type1
and type3 both call the same `ChooseStackable` pool; type2 calls equipment.
Do not label the two stackable columns as separate material/card pools. The
stackable loader resolves `stackable.lst`, requires ID >2, positive `[grade]`
and `[creation rate]`, and excludes registry paths beginning `cash/`, `quest/`,
`recipe/`, `temp/`, `event/`, `emblem/`, or `monsterCard/` (case-insensitive).
Creation rate is only an eligibility check in this stackable branch, not the
item's relative weight; candidates within the selected grade/rarity range are
chosen uniformly, with rarity-0 fallback when the requested rarity has no pool.
Monster cards under `monsterCard/` do not become generic candidates by raising
this table's rates or their creation rate. Inspect `.mob`/independent drops or
other concrete grant routes for a specific card. Eligible materials may have
both generic and separately configured sources; don't promise this table changes
world, area-material, template-item or independent branches.

Reference ServerS4A21's generic equipment branch is distinct from `.mob` `[item]`.
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

For `MonsterDropConfig`, `[basis of rarity dicision]` contains four consecutive
rows of seven integers with no leading row count. Flatten whitespace before
grouping; editor line breaks can split rows. Each column is a cumulative upper
threshold for rarity index 0..6, checked in order against an inclusive 1..1000000
roll with `<=`. Differences between clipped consecutive thresholds determine
probabilities; values above the denominator add no interval. This generic
equipment and stackable path always uses row 0, regardless of difficulty or
monster type. Preserve other rows without inventing their runtime roles; the
hell parser has a separate leading-count layout and consumer.

## Coverage and special dungeon rewards

The `itemdropinfo_*` tables do not cover every reward source. Close the actual
dungeon's `DungeonDropDefinitionCatalog` policy and settlement branch before
applying global settings. Standard policy also allows `.mob` pools, area
materials, independent drops and world drops. Impossible policy permits only
gold, independent and dimension monster-drop sources; classify from `.dgn`
`[impossible dungeon classification]` and verified shared solo/party metadata,
not an "ancient" or event display name. Licensed policy disables ordinary
monster sources and uses `etc/dungeonetc/licensedungeoninfo.etc` rewards.
Dimension dungeons use `etc/dimensiongatedroplist.etc` normal/set chronicle
pools by job/grow type for dedicated monster rewards and free/paid item cards;
their card gold and paid-card cost still use the common settlement configuration.
Hell equipment generation uses `etc/itemdropinfo_monster_hell.etc`, but its
caller can additionally generate independent drops and epic pieces. Determine
event rewards from the concrete dungeon/quest/special-mode caller; an event
name does not select a universal event drop table.

## Hell rarity table parsing

`[dungeon difficulty drop prob]` repeats seven integers: dungeon basis-level
minimum/maximum, then five equipment-trigger rates for dungeon difficulty
indices 0..4. These columns do not select the hell rarity rows. Reference
`HellMonsterDropConfig` uses a fixed base rate of 100, rejects rates <=0,
then accepts `Next(1001) <= rate`; positive rates below 1000 therefore have
probability `(rate+1)/1001`, and rates >=1000 always pass. This is per reward
attempt, not per run or an epic rate. `[drop prob count]` is not read by this
loader; reward attempt count is supplied by the caller. `[item drop rarity
control]` is also not read by this branch. Preserve these fields without
inventing effects. Rarity indices are 0 common, 1 uncommon, 2 rare, 3 unique,
4 epic, 5 chronicle, 6 legendary; reference ServerS4A21's hell roller checks only 0..5,
so the seventh stored threshold does not enable legendary selection.

In the reference ServerS4A21 random hell-equipment branch, candidate grades come
from `EquipmentDropLevelRule.TryGetAtlasGradeRange`: inclusive dungeon minimum
required level through dungeon basis level plus seven, bounded to 1..200.
It filters equipment `[grade]`, not wearable `[minimum level]`. The hell
`[item drop ref table]` is parsed, but the active candidate selector does not
call its range helper. Regions share the positive-creation-rate equipment pool;
compare actual filtered epic ID sets and weights before declaring different
regions' epic lists equal or different. Equal rarity thresholds alone do not
prove equal candidate lists or per-run success; difficulty rates, roll counts,
buff rerolls and independent sources remain separate.

For reference `HellMonsterDropConfig`, `etc/itemdropinfo_monster_hell.etc`
`[basis of rarity dicision]` starts with the row count, followed by exactly
seven integers per row. Whitespace/newlines do not delimit rows; a visually
six-value row consumes the next line's first integer and shifts later rows.
Only each row's first six values are used as cumulative rarity 0..5 thresholds
against a 1..1000000 roll; preserve the seventh value without assigning it
a rarity effect. Hell difficulty 1 selects row 0 (very hard), 2 selects row 1
(hard); this is separate from dungeon difficulty. Validate count and parsed
groups before interpreting probabilities. These are conditional rarity rolls,
not per-monster drop rates; epic-buff rerolls and empty-pool fallback can alter
the delivered quality.
An over-denominator threshold is not intrinsically a zero-probability placeholder:
when reached in an ordered comparison it accepts every remaining roll. It has
no interval only when an earlier threshold already covers the entire denominator.
Preserve the seventh stored value even though this hell consumer checks only
the first six columns.

Reference ServerS4A21 manual hell entry, when the request does not specify mode 1 or 2,
selects A/very hard or B/hard randomly by the nonnegative `Probability`
weights from `etc/hellparty.etc [difficulty]`; do not assume equal odds.
An explicit mode request is honored on the ordinary path. A successfully
prepared gorgeous challenge forces very hard. The selected mode is stored
on the run and reused; the rarity table does not choose the entry mode.

## Ordinary clear-card items

The clear-card `[basis of rarity dicision]` has no leading row count; its parser
keeps a flat threshold array and the roller checks every supplied threshold in
order. Do not impose the hell table's seven-column row grouping here. The active
array length determines the available rarity indices; preserve its source shape.

Ordinary settlement cards use `etc/itemdropinfo_clearreward.etc` in reference
ServerS4A21. `[drop prob]` holds named profiles of level-min/max/probability triples.
Free item cards use `default`; paid cards use `event`. Their threshold is
`standardRate/100 * (baseProbability * mapRate + difficultyBonus) * (1-deathPenalty)`,
clamped to 0..10000 and compared to `Next(10000)`. The difficulty bonus is
additive, from `[dungeon difficulty drop bonusrate]`. Free mapRate uses visited
room fraction and `[reward item rate per map max count]`/10000 when present;
paid mapRate is `[gold card create rate]`.
Both ordinary free and paid item cards then use the same `GenerateConfiguredItem`
rarity/type/grade selection path. Shared rarity thresholds mean the same quality
roll distribution after the appearance check, not equal overall per-card item
or rarity success rates. Quote profile rates as base thresholds rather than
final percentages without accounting for map rate, bonuses, penalties and pools.

After a hit, `[drop item type prob]` supplies relative type weights; the current
generator supports types 2 (equipment) and 4 (avatar), not types 1/3.
`[basis of rarity dicision]` supplies cumulative thresholds against a 1..1000000
roll, not independent weights. Differences of adjacent thresholds define each
rarity interval; thresholds above 1000000 add no probability. The grade table
and clear-reward equipment pool then select an item; an empty pool yields no
item and has no lower-rarity fallback. Do not equate a rarity interval with
per-card success. `[item drop rarity control]` is parsed but not used by this
generator; `[drop kind prob]` and blank-item tags are not read by this path.

`[item drop ref table]` triples are dungeon basis level, downward grade span,
and upward grade span. Candidates use equipment `[grade]` in the half-open
range `[basisLevel-down, basisLevel+up)`, restricted to 1..200; do not substitute
`[minimum level]`. The pool requires positive `[creation rate]` and grade,
with rarity 0..5; creation rate is the relative item weight. Avatar and ordinary
equipment pools are separate. Changing equipment creation rate also affects
the shared generic equipment pool, so it is not a clear-card-only control.
`[gold card cost table]` pairs are dungeon basis level and paid-card gold cost;
lookup uses the exact level, else the closest lower entry (or the lowest entry
if none is lower). `[drop prob count]` and `[chronicle set reward rate]` are
not read by this ordinary-card parser/generator. Preserve unsupported fields
instead of assigning them invented runtime effects.

## Quest drops

In reference ServerS4A21, `.qst` `[enemy reward item]` repeats eight integers:
enemy code, enemy type, dungeon ID, difficulty, item ID, attempt count,
probability percent, held limit. Enemy types are 1 monster, 2 AI character/APC,
and 3 passive object; match both code and type exactly. Dungeon `-1` permits
any dungeon; negative difficulty permits any difficulty, otherwise it must
match exactly. Only active registered quests provide candidates. This branch
uses the same probability/held-limit rules below as `[monster reward item]`.
For `[type] [seeking]`, `[int data]` supplies item-ID/required-count pairs;
the requirement and held limit have different roles. Check the actual objective
against `[condition message]`, which can retain an obsolete collection count.

Trace the caller beyond `QuestDropProvider.RollDrop`: `QuestDropService`
adds the task-assistant benefit when the account has an active devil-contract
quest-assistant slot (index 2), caching eligibility for the run. The reference
`QuestAssistantDropPolicy` adds floor(baseCount/2), plus one with 50% chance
for an odd base count, then clamps to the effective held limit. Thus an even
base yield gains exactly 50% when there is room below the cap. This benefit
is server/account state, not another quantity column in the quest PVF.


In reference ServerS4A21, `.qst` `[monster reward item]` repeats seven
integers: monster ID, dungeon ID, difficulty, item ID, attempt count,
probability percent, held limit. Dungeon/difficulty `-1` means any; monster
ID must match exactly. Only active quests supply candidates.
Nonnegative reward difficulty is an exact index match, not a minimum difficulty;
the caller passes the current run's difficulty unchanged. Ordinary five-tier
dungeons use indices 0..4, so rows covering 1..4 exclude index 0 and include 4.
Do not add a fifth-index row merely because the client shows five choices, or
replace repeated difficulty rows with repeated wildcard rows: each matching row
becomes a candidate and can change selection behavior. Resolve actual map spawns
when a covered difficulty still gives no item, rather than assuming a higher tier
is excluded.
First confirm the definition path is registered in `n_quest/quest.lst`;
reference ServerS4A21 loads quests through that registry, so an archived but
unregistered `.qst` is not an active drop source.

Each attempt succeeds with `Next(100) < probability`; increasing count adds
attempts, not guaranteed items unless probability is 100. Held quantity is
capped by the smaller applicable value of the row limit and `[seeking]`
`[int data]` required quantity. Raising only the row limit does not raise the
collection goal. Preserve scope and collection targets for yield-only edits.
Test while the quest is active and held quantity is below its effective cap.

## Monster item pools

`.mob` `[item]` repeats item ID / weight pairs, closed by `[/item]`.
Reference ServerS4A21 first rolls a shared 10% pool trigger, then selects one
item by relative weight and emits one unit. Area material, when enabled and
configured, is merged into this pool with weight 100. Evaluate the merged
pool and dungeon drop policy; a raw weight is not an absolute probability.

Increasing an item's weight only increases its share of the pool. It cannot
raise the shared trigger rate or emitted quantity. A single-entry pool gains
no share from a larger weight unless another source contributes entries.
Higher absolute frequency/quantity requires tracing a supported independent
drop configuration. If no PVF route meets the request, explain the server-only
limitation for the user to handle; do not change reference code. Monster
templates can be shared by multiple dungeons; close map/monster references
before claiming an edit affects only one dungeon.

These runtime meanings apply to reference ServerS4A21. Moving the same
implementation to another computer does not change them; if the implementation
changes, verify the affected loader/caller before promising the same behavior.

The shared trigger is `MobItemDropRate / DropDenominator` in
`ServerS4A21/Server/DfoServer/Game/Dungeon/DropGenerator.cs`, not a PVF field.
That hardcoded rate is outside PVF edit scope. Report its shared impact for
the user; do not modify, rebuild or deploy reference-server code. For a single item's higher rate, the server
also supports direct-item entries in `etc/independent_drop.etc`; these are
additional rolls, so account for existing pool drops and possible double drops.

## Breakable-object specific materials

Close dungeon registry -> `.dgn` `[special passive object item]` groups -> maze
map registry -> `.map` `[special passive object]` spawn actions. Each DGN group
is `groupIndex levelOverride itemCount (itemId weight)*`; itemCount counts
candidate pairs, not units. Indices must be consecutive from zero. Current
parser disables the definition on duplicate tags or malformed groups.
This metadata path reads only the first data line, so later numbered groups
on separate lines are not loaded. Verify both tag count and parsed groups
before interpreting configured probabilities.
Repeated tags can appear after different `[maze info]` / `[quest connection]`
segments in source DGN layout. Report that context separately from the current
parser's single-definition limitation; do not call the PVF inherently corrupt
or recommend merging/removing tags without verifying the deployment's maze
scope. Multiple numbered groups inside one tag are distinct map-action pools,
not additional copies of the same reward or unit counts.
An `[item]` spawn action has `groupIndex specificAttempts randomAttempts p1 p2`;
reference ServerS4A21 specific branch uses the first two integers to select a group and
repeat independent picks, each emitting one unit. Random attempts use
`etc/itemdropinfo_object.etc` separately. Do not change unrelated actions or
claim a repeated attempt count guarantees that many units.
Specific selection rolls `Next(10001)` against cumulative nonnegative weights
using `<`; a single candidate weight w <=10000 has chance w/10001 per attempt.
Weights are not normalized; leftover interval produces no item, and preceding
candidates affect later intervals. Raising DGN weight changes probability;
raising the map action's specificAttempts changes number of rolls, not stack
quantity. Maps may be shared across dungeons. Distinguish breakable material
sources from `.mob`/independent drops, package rewards, recipe inputs and entry
costs; a numeric mention or item explanation is not proof of a grant.

## Direct independent drops

Reference ServerS4A21 direct rows in `etc/independent_drop.etc` have 17 integers:
`0 monsterId itemId p0 p1 p2 p3 p4 c0 c1 c2 c3 c4 levelMin levelMax difficulty 0`.
Probabilities use denominator 1000000 and difficulty index 0..4. The generator
reads only c0 in reference ServerS4A21 for quantity, even in parties; preserve other columns.
Level bounds are dungeon basis levels and applied only when both are positive;
difficulty -1 permits all difficulties. There is no dungeon-ID column in this
direct format, so a matching monster template can drop in multiple dungeons.
The branch must be enabled by the dungeon drop policy. Bind actual `.mob` IDs
from `monster/monster.lst`, not NPC/APC IDs or names. Human-looking enemies
are not proof of an APC; actual AI-character actors need separate path tracing.
Direct rows are additional item rolls and do not replace `.mob` pools or
require a quest. Verify actual spawn IDs, count and frequency in game.
