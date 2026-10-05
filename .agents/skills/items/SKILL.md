---
name: items
description: >-
  Inspect or edit S4A21 次元彼端 item definitions (.stk/.equ): materials, potions, NPC
  item prices, expiry, binding, package rewards, quest objectives/rewards, vault tickets, and new IDs.
  Use for item properties or opening/use effects. For NPC listings also read
  npc-shop; for CERA listings, prices, pages, or contracts read cera-shop.
---

# A21 materials, consumables, stackables

Scope: S4A21 次元彼端. Use [../SKILL.md](../SKILL.md) for target selection,
no-target answers, reference-only investigation and packed-write validation.

## Edit boundary and read-back

Resolve `.stk`/`.equ` through their registries, quests through `quest.lst`,
and each referenced reward through its item registry. Change only requested
definition fields; preserve use type, unrelated effects, prices and rewards.
Check shared shop/recipe/package/quest consumers before changing definitions.
Read back field types, units, reward-group alignment and references; compare
all out-of-scope content. Expiry, accepted quest progress and title instances
can be saved state and are not automatically migrated by a PVF edit. Explain
the impact and human repair option; never change database data for the user.

Do not paste examples from this file as a complete `.stk`. NPC listing syntax:
[../npc-shop/SKILL.md](../npc-shop/SKILL.md). CERA products and service contracts:
[../cera-shop/SKILL.md](../cera-shop/SKILL.md).

## Disjointer machine endurance (reference ServerS4A21)

`character/expertjob/disjointer.exj` controls profession machine endurance,
not equipment `.equ` durability. `[endurance initial value]` initializes new
machine state. `[endurance repair cost]` repeats full-repair gold cost and
maximum endurance pairs, ordered by machine grade starting at 1; there is no
grade-ID column. Preserve costs when increasing only capacity, and align the
initial value with grade-1 capacity. `[endurance reduce]` is an inclusive
minimum/maximum random loss per successful disassembly; zero/zero is accepted.
Repair restores up to the configured capacity; upgrade requires current-grade
full endurance and sets the target-grade capacity. Existing current endurance
is saved character state, so raising PVF capacity does not automatically refill
it. Test repair and disassembly after loading matching PVFs and restarting.

`[gain exp]` gives inclusive minimum/maximum profession experience per successful
machine disassembly. `[expertness exp]` is parsed in three-token groups; only
the first token is used as a cumulative experience threshold, and thresholds
must be strictly increasing and nonnegative. Preserve the second token and
backtick title. Reference `GetExpertJobLevel` starts at 1 and adds one for every
reached threshold; do not label rows as per-level incremental costs or assume
the last row equals maximum machine grade. Machine grade is upgraded separately
and still requires full endurance, character-level eligibility and upgrade gold.

## Locate

1. Stackable: `stackable/stackable.lst` → `stackable/**/*.stk` (materials, potions, quest items, packages, emblems, …)
2. Gear / titles / avatars / creatures: `equipment/equipment.lst` → `.equ`
3. To sell through an NPC: put the ID in the target `.shp` `[item list]`. For CERA sales, follow cera-shop's product mapping.

IDs come from `.lst`. Editing `[name]` or file contents does not allocate an ID.

## Remove unwanted definitions

Unlisting and definition cleanup are separate scopes. For authorized cleanup,
resolve exact ID/path pairs and scan all retained PVF files for incoming item-ID
and definition-path references, including nested packages, shops, quests, and
text scripts. In this A21 Type 1 format, integer tokens use type 0; type 2 is
floating-point. Type 3 file payloads are UTF-16LE text. Classify matching numbers
in context rather than assuming every occurrence is an item reference. Exclude
definitions being removed from the incoming-reference check.

Include online-time rewards in `etc/pcroomtimepoint.etc` (`[daily reward items]`
and `[period reward item]`) in the reference scan. Remove an unwanted reward's
complete entry, not just its item ID; preserve adjacent rewards and their fields.
For retained quests and shops, remove only the matching reward entry or listing;
their own registry rows remain valid and do not need removal.

Remove each unused registration and its matching definition together. Preserve
all retained pairs and their order. Packed deletion must rebuild file/path indexes
and the archive hash table; an empty definition is not file removal. Reopen and
verify absent paths/IDs, exact retained payloads, and the expected file-count
decrease. Include deleted paths and the changed registry in the change log.
PVF reference checks do not inspect existing inventory/mail/database instances.

`[stackable type]`: `[material]` is usually a material; potions are often `[waste]`, with other usable types. Keep the item's original type. Do not change type just to set a price (inventory tab and use rules follow type).

## Shared fields

| Goal | Tags | Notes |
| --- | --- | --- |
| Name / text | `[name]` `[explain]` `[flavor text]` | text ≠ effect |
| NPC buy gold | `[price]` | preferred for normal NPC shops |
| Recycle-related | `[value]` | not the full sell price; see formula |
| Extra gold on exchange | `[add price]` | only on a valid `[need material]` path |
| Bind | `[attach type]` `[trade limit max]` `[impossible contents]` | copy a same-kind sample |
| Stack / weight | `[stack limit]` `[weight]` | raising stack may merge existing piles |
| Level / quality | `[minimum level]` `[grade]` `[rarity]` | `grade` ≠ rarity |
| Icon | `[icon]` `[field image]` `[icon mark]` | text edits do not create art |
| Absolute expiry | `[expiration date]` | CST (UTC+8) |
| Days from create | `[usable period]` | non-negative int from **create time** |

If the tag already exists, change the value; do not add a second copy.

## A21 binding and trade type

`[attach type]` uses the following values in S4A21 次元彼端 PVF. Do not
infer the meaning from the English word `trade`.

| Value | Meaning |
| --- | --- |
| `[free]` | Unrestricted |
| `[sealing]` | Sealed |
| `[trade]` | Untradeable |
| `[account]` | Account-bound |
| `[trade delete]` | Cannot be traded or deleted |
| `[sealing trade]` | Sealed and untradeable |

To allow unrestricted trading, change the existing `[attach type]` value to
`[free]`; `[trade]` means untradeable. Keep backticks and do not duplicate the
tag. Check other applicable trade restrictions separately; this field alone
does not establish every runtime restriction.

## A21 price (plain stackable)

- **Buy**: `[price]` first; else positive `[value]`. Neither → NPC shop buy price `0`.
- **Sell**: `floor(value / 5)` first; else positive `floor(price / 5)`. Do not store the gold the player should receive in `[value]`.
- **Material-exchange gold**: with a valid `[need material]`, `max(0, price + add price)`. **No** `[value]` fallback; missing `[price]` → gold part `0`.

Arithmetic example only: `price=100`, `value=10` gives buy 100 and sell 2;
with only `price=100`, sell falls back to 20. Read the actual target values;
these are not assignments to a named item or a pricing policy.

For reference ServerS4A21's NPC exchange path, `[need material]` is one effective **material ID, count** pair: it reads only the first two values. Do not add further pairs expecting them to be charged. Do not confuse the pair with the shop item's own ID. A21 samples close `[/need material]`; if a file has no closer, follow neighbors — do not mix styles.

To convert an NPC material exchange to ordinary gold sales, remove the complete
`[need material]` block, including its closer, then update or add one `[price]`.
Placement before `[icon]` is a formatting convention, not a price requirement.
When that convention is requested, move the existing complete `[price]` field
or insert one missing field immediately before `[icon]`; never duplicate it.
Keep `[consume item]`, use effects, `[value]`, and shop item lists unchanged.
Item-definition pricing applies wherever that item is sold; if `[value]` is absent,
the new `[price]` also changes the ordinary stackable sell-price fallback.

Equipment recycle uses the server equipment rate, **not** `value ÷ 5`, with a minimum. Equipment buy still prefers `[price]`, else `[value]`; with valid `[need material]` use `price + add price`.

`[cash]` and `[medal]` are item metadata, not reference ServerS4A21's generic CERA or medal price setters. For CERA price/page placement use [cera-shop](../cera-shop/SKILL.md); for medals or another currency, trace the target handler before editing any field.

## Expiry

- `[usable period] 7`: new instance lasts 7 days from create.
- `[expiration date]`: `` `yyyy-MM-dd HH:mm:ss` ``, `yyyy-MM-dd`, parseable `yyyyMMdd`, or Unix seconds; CST.
- Both present: server prefers positive `[usable period]`.
- Expiry is stored on the **instance**. Editing PVF does not refresh old items.

Distinguish normal item creation from rental grants. Reference ServerS4A21 rental
handler explicitly supplies an instance expiry of current Unix time plus
`RentalWeaponRequestCodec.RentalDurationSeconds` (86400); this overrides the
ordinary equipment expiry resolver. A `.equ` usable-period change therefore
does not control that rental grant's actual lifetime. For newly claimed items
already shown expired, check grant path, instance timestamp, server/client
clock and protocol consumption before treating it as a stale PVF date.
Removing usable period is not a repair when the requested rental duration
must remain; do not claim a PVF-only fix without identifying a relevant field.
- `[stat change duration]` = effect length; `[cool time]` = use cooldown (often ms). **Neither** is bag expiry.

To drop expiry on future instances: delete the whole `[expiration date]` (not empty string, not a far date) and confirm there is no `[usable period]`. Uncovered new instances get `ExpireTime=0`.

For a name-prefix weapon expiry edit, resolve equipment.lst entries, match the
actual `[name]` prefix, and require `[equipment type]` `[weapon]`; a name alone
does not establish the slot. Remove both applicable expiry tags and leave
already-unlimited matches unchanged. Type 1 payloads use five-byte tokens;
removing only the expiry tag/value tokens preserves all other tokens and string
offsets. Reopen the output and compare every file payload against the source,
allowing differences only at the planned paths and removed token ranges.

For event potions, preserve `[stat change duration]` / `[cool time]` during
expiry-only edits. Inspect `[usable event]` and `[item category]` separately;
removing expiry does not lift event limits. Verify a newly acquired instance.

## Materials

Price-only edits: price tags only. Before changing use, search recipes, quests, shops, exchanges for that ID. Do not retag a material as a consumable to “make it listable”.

### Automatic pickup and global drops

For A21 materials picked up on proximity, inspect `etc/autorooting.etc`
`[auto rooting index]` after resolving their IDs through `stackable.lst`.
To disable listed items' automatic pickup, remove only those integer ID entries
and preserve all other entries and the paired closing tag. Gold ID `0` is a
separate entry; do not replace unwanted IDs with `0`. Verify manual pickup and
proximity behavior after restarting the client with the edited archive.
This list is separate from drop generation: reference ServerS4A21's
`etc/worlddrop.etc` `[world drop]` entries use monster-level groups and weighted
item pools, which can make a material appear across unrelated dungeons.
Changing pickup membership does not disable those drops. A logged `GET_ITEM`
proves a client pickup request, not whether the player pressed a pickup key.

## Consumables

For `[teleport potion]` items, resolve `[int data]` town IDs through
`town/town.lst` to `.twn` definitions and inspect `[name]` and areas.
Channel names do not identify the current town. A disabled destination alone
does not prove a missing definition or texture; compare from another town.

| Topic | Common tags |
| --- | --- |
| Cooldown | `[cool time]` `[cooltime group]` `[cooltime maintenance]` |
| Effect | `[hp recovery]` `[mp recovery]` `[stat change]` `[stat change duration]` `[effect maintenance]` |
| Limits | `[usable job]` `[action usable place]` `[impossible contents]` |
| Uses / purchase cap | `[total usable count]` `[daily purchase limit]` |

Effect layouts differ per potion: copy a same-kind item; change only values you understand. Flavor like “reduces cooldown” in `[explain]` does not change mechanics (e.g. `2600021`).

For maintained potion effects, reference ServerS4A21 requires a positive
`[stat change duration]` when `[effect maintenance]` exists and uses that duration
for its effect-state deadline. `[cool time]` is the item's reuse cooldown.
For this A21 cooldown-reduction potion, the maintained-effect shape
`[effect maintenance] 1 1 1 0` with ``[stat change duration] 1800000 `myself` ``
is usable without replacing the original special effect. Recheck real skill
cooldowns and map transitions when changing duration; an icon alone is insufficient.
Do not generalize this result to other special consumables without checking them.

`[cooltime group]` is a shared item-reuse cooldown group ID, not a duration or
an effect group. Matching groups associate cooldowns but do not make differing
`[cool time]` values equal. Reference ServerS4A21 checks same-item and same-positive-group
active cooldown states when its `[cooltime maintenance]` path is enabled; a group
tag alone does not enable that server path. Keep one copy of each cooldown tag.

For an ordinary stackable with neither price field, NPC listing alone leaves
buy price at 0. Add `[price]` only if charging was requested; `[value]` also
affects recycle. Set expiry only when included in the requested change.

## Other existing types

| Type | How to spot | Notes |
| --- | --- | --- |
| Quest item | `[stackable type] [quest]`, e.g. `3072` | search quest refs first |
| Expert-job material | `[material expert job]`, e.g. `2610045` | `[expert type]` and expert shops; do not retag `[material]` |
| Emblem / gem | `[avatar emblem]` / `[flag gem]` | `[enchant]`, target type, equip limits |
| Weapons / armor / jewelry | `.equ` + `[equipment type]` | durability, `[repair price]`, sets; recycle uses equipment rate |
| Title / avatar / creature | `[title name]` `[coat avatar]` `[creature]` | not ordinary weapons |

Copy a same-kind `.equ`/`.stk`. Do not paste potion field blocks. Equipment may also have `[price]`/`[value]`/expiry with the same formats, but wear / durability / recycle rules differ.

## Quest objectives and completion item rewards

For reference ServerS4A21 `[hunt enemy]` quests, `[int data]` repeats five integers:
dungeon ID, minimum difficulty, enemy code, enemy type, required count.
Quantity-only edits change the fifth value of the relevant group and synchronize
the corresponding `[condition message]`; preserve the other four values,
prerequisites and rewards. Enemy type 3 denotes a passive object and uses
client-reported progress in this implementation, not ordinary monster-kill
counting. Resolve the registered quest before interpreting its target layout.

Accepted hunt/seeking progress is stored in `character_active_quests`, separately
from PVF targets. Lowering requirements can leave old remaining counts above
the new target, producing negative displayed completion (required minus remaining).
This is not negative kills. A PVF edit does not migrate accepted progress or
reissue rewards. Explain the affected state; all database repair is human-only.
Do not reset unrelated quest/title state or offer a PVF edit as a database fix.
For seeking objectives, the saved first channel is the sum of missing quantities,
not a separate channel for each material; check actual holdings before advising.

Resolve the exact quest name through `n_quest/quest.lst` and its registered
`.qst` `[name]`; a name in dialogue is not a quest-name match. With
`[reward type]` `[item]`, ordinary `[reward int data]` entries are fixed item-ID/count pairs, and
`[reward selection int data]` is a separate selectable reward list. Resolve
each reward ID through stackable or equipment registries before editing.
Keep pair alignment and closing tags; quantity edits do not require changing
the item's definition or the quest's objective `[int data]`. Verify on an
eligible character completing the quest; do not claim prior grants refresh.

Reference ServerS4A21 also parses job-filtered entries as item ID, `[job]`, job ID,
grow type, count; do not flatten them into pairs. Fixed item rewards can use
ID `0` as a gold marker, while selectable rewards cannot. Preserve existing
special entries and confirm the consumer before changing their structure.

### Title-book quest rewards (reference ServerS4A21)

`[reward type]` `[title]` uses the title/achievement completion protocol branch
and skips ordinary inventory reward grants. Close the title-book chain through
`etc/titlebook.etc`, its registered title quest and reward equipment definition.
For a `[clear quest]` wrapper, `[int data]` identifies the prerequisite quest;
the wrapper achievement ID differs from that prerequisite and from the title ID.

Separate quest hand-in, achievement remaining counters and actual title
instances when diagnosing missing titles. Zero counters do not prove an instance
exists. Reference `TitleBookMutationService.TriggerAchievement` creates a title
only on a nonzero-to-all-zero P1/P2/P3 transition; relogging or already-zero
progress is not a verified reissue method. P4's client semantics are unconfirmed.
PVF reward edits do not repair saved instances or automatically replay completed
quests. Report that boundary and teach human repair when requested; never
perform database writes or execute repair scripts. Preserve unresolved state.

## Equipment conversion material costs (A21)

Conversion books use `[emancipate ticket]` to select a conversion type; this
value is not a material quantity. Resolve source equipment through
`equipment.lst` and match its `[emancipate]` / `[emancipate type]` to the book.
The source `.equ` block's `[input]` repeats material-ID/count pairs; lower only
the intended material's count after resolving its stackable registration.
Keep `[output]`, conversion type and other materials unchanged. Apply a
type-wide change to all matching registered source equipment, not output gear.
Reference ServerS4A21 charges those input pairs plus one conversion book;
it ignores nonpositive input pairs and rejects an empty effective input list.
For quantity reductions, prefer positive counts. Load matching client/server
PVFs, restart, and verify both the displayed requirements and actual deductions.

## New items

For new definitions, follow the user's requested naming scheme or the target's
same-purpose neighbors; a personal prefix is not required by the PVF format.
Keep filename sequences separate from allocated IDs. Registry paths are relative
to the registry folder, for example `custom/example.stk` under `stackable/`.
This internal archive directory is distinct from the protected host `custom-pvf`.
Check ID and path collisions in the actual final archive; neither a naming
prefix nor a value fitting int32 proves a whole ID range is safe or reserved.
Validate a new range with a real client acquisition/use test before extending
it. Do not reassign a removed custom ID to an unrelated item while old inventory,
mail or other saved references may still exist.

Need all of: unused ID (re-check the final PVF `.lst`), `.lst` mapping, new definition file. A new name and icon path do not create gameplay; the server must already support the use behavior. Placeholder IDs in examples (`123456789`) must be re-checked.

### Fixed package `[cera package]`

For reference ServerS4A21 ordinary `[booster info] [etc]` pools, the leading
integer is draw count, followed by item-ID/weight/quantity triples. Selection
normalizes positive weights within each eligible group, not against a fixed
100000 denominator. One eligible positive-weight entry is always selected
on each draw. Repeated `[etc]` blocks are independent pools; do not merge
them by tag or leading count. Other booster categories can have different
strides. Resolve reward IDs and verify the source item's use path before
claiming a complete item is usable; special rewards may use dedicated handlers.

`[package data]` repeats **item ID, count**. Positive item IDs only; gold ID `0` is invalid. Do not mix with random `[booster info]`. Avatar packages are often split by job.

Existing A21 packages can also have `[package data selection]`, repeating item
ID/count pairs. Include every such block in the reward closure, alongside fixed
and nested rewards; checking only `[package data]` misses selectable rewards.
Preserve selection behavior when making listing-only changes and verify the
choice interface in-game.

For the reference ServerS4A21 `[RANDOMBOX]` parser, its nested `[int data]` reward
layout starts with three prefix integers, then repeats four-integer groups:
item ID, weight, count, and a preserved fourth value. Validate item references
at those group starts, not every integer; weights are not item IDs. This is a
runtime-specific layout, not a rule for other item types' `[int data]`. The
parser's short-form fallback uses the first item/count pair when no full reward
group resolves. Verify nested rewards through the target's actual definitions
when only restoring shop listings.

A literal `name_ID` is a localization placeholder, not evidence that the reward
definition is invalid. Check actual localization files, package contents, and
explicit series/job information before assigning a display name. A mapping in
`n_string.lst` does not prove the referenced localization file is present.

Minimal fragment (copy remaining fields from a neighbor; not a complete file):

```text
[stackable type]
	`[cera package]` 0
[package data]
	50 1 3037 100
[/package data]
```

Opening should consume the source item and grant the list. Icons may reuse existing art.

### Cash-shop package tables

Product prices, placement, moving between pages, contracts, and catalog migration
are maintained in [cera-shop](../cera-shop/SKILL.md). This skill owns the item's
reward/use definition; load both when the task changes the catalog and its rewards.

### Level-up tickets

For the reference ServerS4A21 ordinary level-up-ticket path, each use grants
one level. Resolve the target definition and read its stack limit; do not assume
one existing ticket's stack limit applies to every ticket.

### Personal vault max ticket

For a personal-vault upgrade route, resolve `cash/safe_upgradekit.stk` and
numbered `safe_upgradekitN.stk` through `stackable.lst`, then close their
`[explain]` capacities against the matching `cerashop.etc` products and prices.
The unnumbered filename is tier 1; subsequent numbered tiers target successive
capacities. In reference ServerS4A21, a recognized higher-tier ticket targets
its capacity directly, and a higher-tier CERA purchase charges that product's
price rather than summing skipped tiers. State this runtime boundary; PVF text
alone does not prove live-server skip behavior. Keep account-vault tools and
`etc/accountcargo.etc` separate from this personal-vault route.

Distinguish inventory ticket use from CERA purchase retries. A ticket whose
target capacity is already reached is not consumed. In reference ServerS4A21,
buying an already-reached tier instead advances to the next tier and charges
its catalog price; if that next-tier product is missing, the implementation
falls back to the clicked product's price. At maximum capacity, purchases are
rejected without payment. Label route totals as sequential-purchase totals,
not as a mandatory cost for a direct-target upgrade.

Character vault in Seria's room, not account vault. The reference runtime
starts at 8 slots and caps at 200. Resolve `cash/safe_upgradekit12.stk` in the
target registry for the max-tier ticket instead of assuming a fixed ID.

For a new ID that should reliably target 200 slots, use a distinct path whose basename remains `safe_upgradekit12.stk`; the server recognizes that basename as tier 12. A non-matching filename can fall back to parsing a supported capacity from the item's Chinese text, but that is less explicit and must be tested in-game. At 200 slots the ticket is not consumed. It does not change inventory, avatar slots, or account vault.

## Verify

1. Client and server load the same packed PVF; restart to clear item cache.
2. Materials: inventory tab, buy price, recycle; if exchange, deducted materials and extra gold.
3. Consumables: buy, stack, use place, cooldown, real effect, recycle; text matches effect.
4. Expiry: check a **new** instance; old items do not prove the new PVF.
5. New IDs: recognized, icon/text OK, package/ticket result matches existing server logic.
6. Quest items / emblems / gear / titles / avatars / creatures: tab, slot, durability, look, and related quest or socket behavior.
