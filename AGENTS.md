# S4A21 PVF

This repo is an agent skill pack for **S4A21 次元彼端** PVF. It is not the unpacked script tree.

Its purpose is repeatable S4A21 PVF inspection and editing by different agents,
with matching Chinese field references for manual maintenance. Scope is the 次元彼端 S4A21
PVF format and the ServerS4A21 implementation documented by these skills.
Changing computers, checkout locations or compatible PVF tools does not change
the documented field meanings. A different server implementation/build requires
checking the affected runtime rules, not assuming all A21-labelled servers agree.

This file is the project map: structure, ID relationships, hard rules, and which scenario skill to load. Scenario how-to lives in `.agents/skills/<name>/SKILL.md` — there is no hub `skills/SKILL.md`.

Write `AGENTS.md` and all `.agents/skills/` instructions in English for AI agents.
Write the root `README.md` and `docs/` in Simplified Chinese for PVF developers.
Preserve literal project names, file paths, PVF tags and required output labels;
keep the English instructions and Chinese field references semantically aligned.

Reply to the user in Simplified Chinese.

## Targets and reference-only projects

- Use the PVF or unpacked tree explicitly supplied/designated for the active task;
  do not silently choose another local pack as a target or baseline.
- Without a designated PVF/tree, answer only from recorded skills, identifying
  the answer as documented rules rather than a check of actual target data.
  If the knowledge is missing, say it is uncertain and no reliable answer can
  be provided. Do not infer IDs, values, schemas or gameplay effects.
- `ServerS4A21` (server), `S4A21ClientPatch` (client patch) and `S4A21GmTool`
  (GM) are read-only references. Trace them when needed to explain a PVF field
  or diagnose a conflict; do not change their code or configuration. Database
  changes are human-only: agents may explain reference code and provide manual
  repair examples, but never mutate data or execute repair scripts for the user.
  Server-only fixes are reported for the user to implement, not included in a
  PVF edit. Their absence is not permission to invent undocumented behavior.
- `custom-pvf` is user-maintained learning/import/share material. Read only
  when relevant; never write, reorganize, rename, delete or automatically apply
  its contents. A PVF edit request does not override this boundary.
- When recorded knowledge conflicts with target content or consumer behavior,
  investigate and explain the affected rule and impact. Confirm a changed edit
  proposal with the user before proceeding; do not silently repair unrelated data.

## Maintainer docs

Chinese learning notes for the human maintainer live in [`docs/`](docs/README.md). Do not load them as edit instructions.

Before delivering a PVF query or edit, persist newly confirmed, reusable rules in the matching `.agents/skills/` topic (or this structural guide) and its Chinese `docs/` page in the **same change**. Include read-only investigations. Keep instructions concise and directly usable, with essential version/runtime boundaries. Omit source narratives, historical comparisons, named reference packs, one-off IDs/counts, and uncertain interpretations. Future agents must not need to resupply a previously analyzed pack. Add new topics to both routing indexes; do not rewrite unchanged knowledge.

## Packed vs unpacked

| Artifact | Role |
| --- | --- |
| `Script.pvf` | Packed archive. Never edit as text; rewrite it only through a PVF-aware reader/writer and with explicit authorization. |
| Unpacked script tree | When supplied, editable `.lst` / `.npc` / `.shp` / `.stk` / `.equ` / … |
| User-designated original / baseline PVF | Preserve as the comparison anchor. Do not assume a file is a baseline from its name alone. |

Confirm the actual target: an unpacked script directory, a packed PVF to query, or a packed PVF the user has authorized for rewrite. For a packed write, use a PVF-aware reader/writer, save to a temporary file, reopen it for validation, then atomically replace only the authorized target. Do not invent an unpacked path when none exists.

Client and server must load the **same** packed PVF after changes. The server keeps process-level metadata caches; restart is the verified way to clear them. Use a reload only when that deployment has a separately verified reload mechanism.

## `.lst` registries

A `.lst` maps `ID → relative path` under that folder. That mapping **is** the ID. Changing `[name]`, `[explain]`, or the filename does not create a new ID.

Name tables (`itemname.lst` and similar) are **not** path registries. If the user gives a name, search names then resolve the ID. If the user gives a numeric ID, resolve it through the correct `.lst`.

The same number can exist in more than one registry. Resolve in the registry that matches the current context.

## Top-level tree

| Folder | Registry | Target |
| --- | --- | --- |
| `npc/` | `npc/npc.lst` | `.npc` |
| `itemshop/` | `itemshop/itemshop.lst` | `.shp` |
| `stackable/` | `stackable/stackable.lst` | `.stk` |
| `equipment/` | `equipment/equipment.lst` | `.equ` (gear, titles, avatars, creatures) |
| `n_quest/` | `n_quest/quest.lst` | `.qst` |
| `monster/` | `monster/monster.lst` | `.mob` |
| `dungeon/` | `dungeon/dungeon.lst` | `.dgn` |
| `map/` | `map/map.lst` | `.map` |
| `skill/` | `skill/skilllist.lst` (then per-job lists) | skill scripts |
| `character/` | `character/character.lst` | character scripts |
| `town/` | `town/town.lst` | town scripts |
| `worldmap/` | `worldmap/worldmap.lst` | world map |
| `etc/` | (no single ID registry) | `.etc` / tables |

## IDs that must not be mixed

NPC ID ≠ shop ID ≠ item ID.

Forward close:

```text
npc/npc.lst  → NPC ID → npc/*.npc
.npc [role] `[item shop]`  → shop ID
itemshop/itemshop.lst  → shop ID → itemshop/*.shp
.shp [NPC]  → NPC ID (back-pointer only; not a substitute for the forward close)
```

Always keep the NPC ID, its role-linked shop ID and each listed item ID separate.

## Layering

- `.shp` lists items only (`[item list]`).
- NPC gold price, sell/recycle, bind, effects, and expiry live on the item `.stk` / `.equ`; CERA-shop price and page placement live in `etc/cerashop.etc`.
- A common A21 shop shape is `[sell info]` → `[tab]` → `[item list]`, optionally `[use category]` / `[category entry]`. Tabless, category-only, and daily-rotation shops also exist; preserve the target shape.
- Do not apply other-version shop syntax (`[sell item]`, `[tab name]`) to this PVF.

## Runtime meaning

Runtime rules in these skills apply to the documented S4A21 client/ServerS4A21
implementation, independently of the host computer. Reference-code behavior
is distinct from PVF syntax and actual target values. Preserve this scope
when interpreting prices, expiry, package grants, CERA purchases and gold drops.
Internal PVF paths are lookup rules; absolute host paths and available tools
must come from the user's target and the execution environment. A new machine
does not require the old checkout, source tree or past attachments to apply
already documented rules. Target values and registrations must still be read
from the supplied archive; do not reuse a previous archive's contents.

Icon / resource paths in Script.pvf do not prove the client NPK exists.

## Next

For edit workflow and hard rules, read [`.agents/skills/SKILL.md`](.agents/skills/SKILL.md). Then load only the domain skill it routes to; do not duplicate workflow rules in this structural guide.

| Task | Domain skill |
| --- | --- |
| Script-path lookup, character/skill-tree registries and configuration entry points | [script navigation](.agents/skills/references/script-navigation.md) |
| NPC placement in towns/Seria room; `.shp` listings, tabs, categories | [npc-shop](.agents/skills/npc-shop/SKILL.md) |
| CERA cash-shop products, prices, pages, contracts, catalog migration | [cera-shop](.agents/skills/cera-shop/SKILL.md) |
| Item definitions, package/quest rewards, expiry, bind, use effects | [items](.agents/skills/items/SKILL.md) |
| Gold drop probability, amount, variance, clear-card multipliers | [gold-drop](.agents/skills/gold-drop/SKILL.md) |
| Global material drops, monster item pools, quest-material drop probability/count | [item-drop](.agents/skills/item-drop/SKILL.md) |
| Dungeon difficulty tables, `.dgn` difficulty fields, comparisons, legacy entrances | [dungeon-difficulty](.agents/skills/dungeon-difficulty/SKILL.md) |

For combined tasks, load each involved domain; cash-shop listing alone does not require the NPC-shop skill.
