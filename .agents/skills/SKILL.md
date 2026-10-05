---
name: pvf-a21
description: >-
  Inspect or edit S4A21 次元彼端 DNF PVF archives and scripts. Routes NPC shops,
  CERA cash shops, item definitions, package rewards, and gold drops to their
  domain skills; covers archive output and change records. Read repo AGENTS.md
  for structure and load only the domains needed by the request.
---

# PVF A21 — how to edit

Structure and registries: repo-root [`AGENTS.md`](../../AGENTS.md). Do not load every domain file at once.

Reply to the user in Simplified Chinese.

## Scope and consistent execution

This task-oriented skill pack covers S4A21 次元彼端 PVF and the reference
ServerS4A21 implementation. With the same input, requirement and implementation,
use the same field meanings, edit boundaries and validation criteria on any
computer; compatible tools and execution steps may differ.

Distinguish three facts: documented PVF syntax/field relationships, actual
contents of the task's PVF, and reference client/server/tool behavior. Examples
and filenames do not establish target values. A tool's partial parser is not
the complete PVF schema; parsed configuration alone is not verified gameplay.

- Use only the archive/tree supplied or explicitly designated for this task.
  No supplied/designated target: answer from recorded skills, state that target
  content has not been checked, and do not choose a local substitute. If the
  required knowledge is absent, say it is uncertain and no reliable answer can
  be provided; do not invent fields, IDs, values or effects.
- Discover compatible PVF tools at execution time. Internal paths are portable;
  drive letters, checkout paths, installed tools and old attachments are not
  prerequisites. If no adequate reader/writer is available, explain the tool
  limitation and stop the dependent read/write, without installing dependencies
  or editing the packed archive as text.
- Use recorded rules directly for their stated S4A21 scope; server source need
  not accompany every edit. Inspect the relevant loader/handler when the server
  implementation changes, the target contradicts a rule, or undocumented
  behavior is needed. Reference projects are `ServerS4A21`, `S4A21ClientPatch`
  and `S4A21GmTool`; use them read-only. Never change reference code/configuration
  or databases. Database changes are human-only, including executing repair
  scripts; agents may explain code and provide examples for the user to run.
  If no PVF-only solution is supported,
  report the limitation and the server/client issue for the user to handle.
- `custom-pvf` is user-maintained and strictly read-only for agents, including
  backups, exports and reports: never write, reorganize, rename, delete or
  automatically apply its contents. It is an optional relevant example, not
  the default baseline or output location.
- When a skill rule, target data and consumer behavior disagree, investigate
  and explain the conflict and affected scope. Confirm a revised edit proposal
  with the user before changing it; preserve unresolved data instead of guessing.
- Resolve target IDs and actual values from the supplied PVF. Topic paths are
  entry points, not proof of a particular file's presence, activation or values.
  Missing/empty files require checking references before choosing an edit;
  do not create them merely to match this index or an external bookmark.
- Close the relevant registry/reference chain; specify fields, units, affected
  records and preserved dependencies before editing. Equivalent requirements
  against the same input and supported implementation must use the same rules.
- Validate parsed changes against the input: only authorized fields/paths may
  differ. Different compatible tools may render whitespace differently; compare
  token types, values, order and references, rather than requiring byte-identical
  repacks. Reopen the output with a consumer-compatible parser.
- Separate archive verification from in-game verification. Report concrete
  read-back results and a reproducible in-game check; do not infer activation,
  resource availability or existing-character state from archive membership.

Maintain procedural instructions in the topic skills and matching Chinese
pages: locate the configuration, interpret its fields, change the requested
behavior, preserve dependencies and verify the result. Path indexes support
discovery but do not replace those instructions. Keep pack-specific findings
and counts in task reports, not as reusable rules. Directly correct proven
mistakes and add newly confirmed reusable knowledge in the relevant skill and
its Chinese page in the same change; no separate approval is needed for that
knowledge maintenance. Chinese pages are concise field references. Database
maintenance notes are for the human user and do not authorize agent mutations.

## Load next

| Task | Read |
| --- | --- |
| External bookmarks, script-path lookup, character/skill trees, events and link-system entry points | [A21 script navigation](references/script-navigation.md) |
| NPC placement in towns/Seria room; shop tabs, listings, categories, `.shp` | [npc-shop/SKILL.md](npc-shop/SKILL.md) |
| CERA cash-shop listings, prices, pages, contracts, catalog migration | [cera-shop/SKILL.md](cera-shop/SKILL.md) |
| Item definitions, NPC item prices, expiry, package rewards, quest objectives/rewards, tickets, `.stk`/`.equ` | [items/SKILL.md](items/SKILL.md) |
| Profession disjointer machine endurance, repair capacity and consumption | [items/SKILL.md](items/SKILL.md) |
| Title-book quest rewards, achievement counters and missing title instances | [items/SKILL.md](items/SKILL.md) |
| Gold drop probability, amount, variance, clear-card multipliers | [gold-drop/SKILL.md](gold-drop/SKILL.md) |
| Global material drops, monster item pools, quest-material drop probability/count | [item-drop/SKILL.md](item-drop/SKILL.md) |
| Map breakable boxes, specific-material probability and attempt counts | [item-drop/SKILL.md](item-drop/SKILL.md) |
| Adventure-group leveling thresholds and character contributions | [Adventure-group progression](#adventure-group-progression-a21) |
| Support-character eligibility and count; distinction from link bonuses | [Support-character configuration](#support-character-configuration-reference-servers4a21) |
| Dungeon difficulty tables, `.dgn` difficulty fields, comparisons, legacy entrances | [dungeon-difficulty/SKILL.md](dungeon-difficulty/SKILL.md) |

For NPC shop + item edits, update the `.stk`/`.equ` first, then the `.shp` listing. For CERA shop + item edits, load cera-shop and items; close item definitions/rewards before listing in `cerashop.etc`. Listing or pricing alone does not require unrelated domain skills.

For skills, dungeon tasks beyond difficulty/entrances, monsters, NUT, or other drops without a domain skill, resolve the matching `.lst` where one exists and read the source; follow the hard rules below. Global `etc/` tables do not have a single registry.

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

## String encoding checks

For this A21 PVF format, Type 1 string references distinguish narrow `sTrA`
from UTF-16LE `sTrW` using the offset's low bit. Narrow-table bytes can contain
legacy GBK; decoding every narrow string as UTF-8 can introduce replacement
characters. Compare raw bytes and source reference type when diagnosing text.
Preserve Unicode references for migrated Chinese text; a writer's string-cache
reuse can select a narrow copy instead of the source's Unicode copy. Check
that forced Unicode writes return an offset with low bit 1; a preference flag
that falls back to the narrow cache is insufficient. Validate
names and descriptions with the consumer's decoder, not only the writer's reader.
Recover damaged text from intact source strings, not guessed substitutions.

## Project output and change records

- Preserve input and baseline archives. Unless the user specifies another destination, save the validated new PVF in this project's `dist/` as `Script_yyyyMMdd_HHmmss.pvf`, using Beijing time (UTC+8). Check for name collisions and choose a new timestamp; never overwrite an existing output. Save a temporary archive, reopen and verify it, then publish the new filename.
- Explicit authorization to update a packed PVF in place overrides the separate-output default. Before editing, back up the current target to the user-designated backup directory with a Beijing timestamp; append a sequence number on name collisions, never overwrite a backup. Verify that the backup matches the current input and retain all earlier target changes rather than rebuilding from a reference pack.
- For actual PVF changes, continue using `dist/改动清单.xlsx` unless the user designates another records directory. In that case, carry the existing workbook history into the designated directory and place new CSV, JSON, Excel and support reports there. Preserve existing worksheets; add a worksheet named for the modification function. A1 contains the delivered PVF filename, A2 a brief change description, and row 3 the detail headers. Column A's required header is `路径`; rows 4 onward contain unique PVF-internal paths with actual changes or additions. Add optional columns only for useful, verified details of special changes (such as allocated IDs or payment rules). Filename and description do not replace the required path list. If the exact function name already exists, use a unique function-based suffix and report the chosen name; never replace history.
- Check that the worksheet's path set matches the actual modification set, including changed registries/configuration. Skill/documentation-only changes do not add a PVF change worksheet. Do not update a historical support-analysis workbook as if it were the change log.
- When delivering changed scripts, export complete definitions from the reopened validated archive under a unique output root, preserving each PVF-relative directory and filename. Reject paths escaping that root. Write readable UTF-8 with the consumer-compatible narrow/Unicode decoder and escaped backticks; reparse exports and compare token types, numeric values and string content, including integral-looking float tokens. Unpacked whitespace may normalize. Keep support files separate from the exported script tree.

## Workflow

```text
read-only close → (after permission) minimal edit → read-back → pack matching client/server PVF → in-game check
```

1. Identify the system, the explicitly designated target and write scope. With
   no target, use the documented-knowledge answer path above instead of editing.
2. Resolve name or ID through the matching `.lst`; read the target file. Do not guess from filenames.
3. List paths, fields, old values, new values, and dependents (shops, recipes, quests, packages).
4. Edit only after permission; only planned fields.
5. Read back edited files and related `.lst`.
6. Report: what changed, what did not, pack/load notes, how to verify in-game, whether old item instances refresh.

## Replies

Lead with: can it be done, which files, main risks, next step. Attach IDs, paths, and raw tags when needed.

Label documented rules, target read-back and in-game validation separately.
For unknown fields or unsupported PVF-only behavior, state the specific limit;
do not turn a lookup hint into a definite effect or claim unperformed tests.

## Adventure-group progression (A21)

Reference ServerS4A21 loads `etc/linksystem/charactermanage.etc`.
`[point bonus]` contains level-min/level-max/per-level-contribution triples;
each undeleted account character contributes the sum of all configured levels
at or below its current level. This is not a separately accumulated dungeon
experience counter. `[manage level point]` lists cumulative point thresholds
for group levels starting at 1; keep them nonnegative and strictly increasing.
Lower these thresholds for easier progression without changing character
contributions or benefits. `[manage level max]` caps the result; `[exp bonus]`
and `[gold bonus]` set character reward benefits, not group upgrade costs.
`[manage option]` is a separate benefit field; preserve it for threshold edits.
Tables are process-cached; load matching PVFs and restart, then return to
character selection/login to verify the recalculated account group level.

## Support-character configuration (reference ServerS4A21)

Keep three systems separate: adventure-group progression in
`etc/linksystem/charactermanage.etc`; low/high link experience/gold bonus sections
in `etc/linksystem/characlinksystem.etc`; and support eligibility in
`etc/characlinksystem.etc`. Similar filenames do not make them interchangeable.
Reference `StrikerSkillDataProvider` requires seven integers in
`[2nd link character info]` and uses column 7 as the minimum support level,
falling back to `[1st link character info]` only when the second section has no
valid positive level. It reads `etc/linksystem/striker.etc [striker combo]` as
maximum active support count. Preserve unrelated bonus, timing and skill fields;
read back changed values and verify support selection/cap in-game.

## Dungeon difficulty and entrances

Follow [dungeon-difficulty/SKILL.md](dungeon-difficulty/SKILL.md) for global and independent difficulty tables, archive comparisons, and legacy dungeon entrances.

## Abyss unlock investigations (A21)

- Close the chain `n_quest/quest.lst → registered .qst → worldmap/*.wdm [hell quest]`. An archived `.qst` with no registry row does not prove its task is available; normalize registry path separators before comparing.
- Compare `[level]`, `[type]`, `[int data]`, `[pre required quest]`, `[reward type]` and `[reward int data]` together. A dialogue introduction with item rewards is not an unlock task granting `[hell challenge]`.
- Establish prerequisites from the actual `[pre required quest]` references. An empty block does not make an earlier dialogue quest mandatory; do not infer a chain or minimum level from episode names, filenames, or display order.
- Resolve task participants and targets in their own registries: `[npc index]` / `[complete npc index]` through `npc/npc.lst`, dungeon and monster targets through their respective registries, and required/reward items through item registries. Interpret `[int data]` according to the task `[type]` and same-purpose neighbors; a condition message can disagree with a modified objective.
- Restore region gates and removed quest registry rows together when reversing a shared unlock task. Append only the required missing rows; preserve unrelated target-only registrations rather than replacing the whole registry. Inspect baseline dangling quest references separately; do not silently activate unregistered legacy tasks.
- `etc/hellparty.etc [difficulty]` controls a separate deep-abyss encounter configuration; distinguish its restoration from unlock-flow restoration. Verify character completion state in-game; restoring PVF does not itself clear saved quest progress.
