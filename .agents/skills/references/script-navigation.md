# A21 script navigation

Use this index only to locate S4A21 次元彼端 scripts and their registry chains;
use the shared/topic skills for field meanings, units, changes and verification.
These are documented entry points, not a target inventory or proof of activation.
Apply [../SKILL.md](../SKILL.md) for target/no-target answers and read-only
references. Do not select a local pack when no target was supplied/designated.
Host paths and tools are discovered at execution time. For modified archives,
use the actual registered paths and confirm the schema before editing.
Resolve registered definitions through their `.lst`; compare case-insensitively
after normalizing separators. A bookmark caption is not a schema or an ID.

## Character and skill closure

- Start with `character/character.lst` and `skill/skilllist.lst`, then follow
  the per-job skill list. Do not derive registry filenames from the job label.
- For `[demonic swordman]`, the character definition is
  `character/swordman/dsswordman.chr` and the skill registry is
  `skill/dsswordmanskill.lst`. Skill definitions retain the
  `skill/demonicswordman/` directory.
- SP and TP presentation use `clientonly/skillshoptreespindex.co` and
  `clientonly/skillshoptreetpindex.co` `[skill tree]`; their references are
  relative to `clientonly/`. The dark-knight UI uses
  `skilltree/demonicswordman_sp.co` / `skilltree/demonicswordman_tp.co`.
  A UI tree is not a skill definition or the skill-level data.

## Configuration entry points

| Inspect | A21 path | Sections to locate |
| --- | --- | --- |
| CERA catalog | `etc/cerashop.etc` | `[item]`, `[avatar]`, `[premium]`; use cera-shop |
| Vending-machine groups | `etc/pcroom.vm`, `etc/pcroom4.vm` | `[item group]`, `[group num]`, `[material]`, `[output]` |
| Level-up reward event | `event/levelupreward.evt` | `[level up]`, `[grow type]`, `[send mail]`, `[inven]` |
| Attendance event | `event/attendance.evt` | `[event]`, `[reward]`, `[final reward]`, `[send mail]` |
| Event-list window | `event/eventlistwindow.evt` | `[entry]`, `[event index]`, `[event info]`, `[link popupwindow type]` |
| Unlimited-challenge rewards | `etc/unlimitchallenge.etc` | `[onoff]`, `[server_id]`, `[level]`, `[reward_server]`, job sections |
| Dungeon/quest schedule | `etc/chn_schedule.etc` | `[dungeon]`, `[quest]`, `[common schedule]`, `[dungeon minimap]` |
| Loading messages | `etc/loadingadvermessage.etc` | `[loading message]`, `[dungeon loading message]`, `[town loading message]` |
| Item reset/refill/mail | `etc/chn_server_limititemusageinfo.etc` | `[reset item]`, `[refill item]`, `[charac daily item mail]`, `[account refill item]` |
| Reinforcement | `etc/upgrade.etc` | `[table]`, `[table type]` |
| Amplification | `etc/amplifyupgrade.etc` | `[table]`, `[cost]`, `[cost weights by rarity]`, `[stat weights by rarity]` |
| Separate upgrade / forging | `etc/upgrade_separate.etc` | `[table]`, `[separate upgrade max]`, `[item weights by grade]`, `[separate upgrade effect]` |
| Amplification options | `etc/amplifyitem.etc` | `[option mapping table]`, `[amplification rate by rarity]`, `[option data]`, `[purify material]` |
| Secret shop | `etc/secretshop.etc` | `[dungeon npc]`, `[level section]`, `[level npc]`, `[cash item]` |
| Quest-point shop | `etc/questshop.etc` | `[quest status]`, `[quest status info]`, `[level info]`, `[quest point]` |
| Premium definitions | `etc/premiumlist_new.etc` | `[type]`, `[target item]`, `[attr]`, `[term]`, `[items]` |
| Premium presentation | `etc/premiumserviceeffect.etc` | `[premium service]`, `[type]`, premium image indices |
| SP table | `etc/sptable.etc` | `[sp table]` |
| Equipment-set mapping | `etc/equipmentpartset.etc` | `[equipment part set]` |
| Effect equipment sets | `etc/equipmenteffectset.etc` | `[effect equipment part set]` |
| Account storage | `etc/accountcargo.etc` | `[required level]`, `[upgrade info]` |
| Adventure-group progression | `etc/linksystem/charactermanage.etc` | `[point bonus]`, `[manage level point]` |
| Link reward bonuses | `etc/linksystem/characlinksystem.etc` | `[link level]`, low/high link exp/gold sections |
| Support eligibility | `etc/characlinksystem.etc` | `[1st link character info]`, `[2nd link character info]` |
| Support count/timing/skills | `etc/linksystem/striker.etc` | `[striker combo]`, `[common cool time]`, striker skill sections |

Read actual records and same-purpose neighbors before interpreting units,
weights, maximums or toggles. Event records and presentation entries need their
registration/activation and consumer verified before claiming availability.
Do not equate an event-list window entry with a scrolling announcement, or
different reward systems with interchangeable level-up mail implementations.

Field meanings and reference-server support selection are in the shared
skill's adventure-group and support-character sections. The locator alone
cannot establish that a retained file is consumed.

## Bookmark verification boundaries

- Check archive membership, then readable nonempty content, registry links and
  consumer use separately. A retained empty file does not establish active data.
- Files from other versions, custom example items and locale-specific `.str`
  paths are lookup hints only. Do not create missing files to make a bookmark fit.
- Resolve NPC shops forward from `npc.lst → .npc [role] → itemshop.lst → .shp`;
  a correctly named `.shp` or its `[NPC]` back-pointer is insufficient alone.
- Drop-dictionary display data, drop registries and actual generation tables
  are different layers; use item-drop for the active drop source.
