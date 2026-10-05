---
name: npc-shop
description: >-
  Edit S4A21 次元彼端 NPC shops in itemshop/*.shp: tabs, item lists, job categories,
  listing, new shops, and NPC placement in towns or Seria's room. Use for
  adding town NPCs, NPC shop, itemshop, .shp, NPC tabs, [item list],
  or [use category]. NPC price lives on the item file. CERA cash-shop requests
  belong to cera-shop. Read repo AGENTS.md first.
---

# A21 NPC shops

Scope: S4A21 次元彼端. Apply the target/no-target and read-only reference rules
in [../SKILL.md](../SKILL.md) before using the schema below.

## Edit boundary and read-back

Close NPC registry → `.npc` shop role → shop registry → `.shp`; item IDs close
through stackable/equipment registries. For listings, change only requested
tabs/categories/item IDs, preserving role links, types and unrelated lists.
For placement, change only the intended map actors/coordinates; check shared
town/map references. Prices, effects and expiry are item-definition edits.
Reopen and compare the planned paths, item sequence and reference links;
verify appearance, tabs and actual purchase separately in-game.

Tab = `[tab]`. Category = `[use category]` / `[category entry]`. Not the same layer.

Change the list: edit `.shp`. Change buy/sell/exchange: edit the item file — [../items/SKILL.md](../items/SKILL.md).

CERA cash-shop product records, prices, and pages: [../cera-shop/SKILL.md](../cera-shop/SKILL.md).

## Locate

Do not mix the three numbers. Forward close is in [`AGENTS.md`](../../../AGENTS.md).

Read the task target for actual NPC/shop IDs; filenames and example IDs are not registry authority.

Other entries may be `[product item]` or `[secret shop]`. A static secret-shop close does not mean it appears in-game.

New shop: write `itemshop/itemshop.lst` and the NPC `[item shop]`. Existing shop: keep those ID links.

## NPC placement versus shop registration

`itemshop/itemshop.lst` registers shops; it does not place NPCs. In this A21
map format, a `.map` `[NPC]` list uses five tokens per actor:
`NPC_ID`, backtick direction (`[left]`/`[right]`), X, Y, and an integer flags
field, closed by `[/NPC]`. Preserve the target map's flags convention; do not
invent its meaning. Resolve NPC IDs through `npc/npc.lst`.

For an existing NPC, append a placement to the active room/town map rather
than allocating a new NPC or shop ID. A new NPC definition needs a free ID,
registry entry, `.npc`, and map placement; shop behavior additionally needs
the `.npc` role/shop link and registered `.shp` from the forward closure.

Trace a town's registered `.twn` area map references to actual archive paths;
town references may be relative to `map/` rather than archive root. Seria-room
archives can retain both `map/common/` and `map/town/common/` gate maps, plus
normal/PVP and event variants. Confirm which the target client loads before
choosing a file; basename matches or missing `map.lst` rows do not prove a town
map is unused. Copy the existing `[NPC]` shape and preserve existing actors.
Verify position, walkability, appearance, interaction, and shop behavior in-game.

An NPC-ID placement search can match ordinary, mirrored, dream and event maps.
Close each candidate against registered `.twn` area references before naming
the active town map; duplicate NPC coordinates do not identify the loaded file.
Body-width descriptions are approximate placement requests, not a fixed PVF
unit: propose an X offset with the original Y and verify spacing in-game.
For a horizontal move, decrease X to move left and increase X to move right;
preserve Y, direction and flags for position-only edits.

## Outer skeleton

When diagnosing an invisible custom NPC, separate map placement/registry,
appearance resources and shop parsing. A `.shp` containing NPC tags such as
`[field animation]`, `[role]` and `[dialog]` without a shop sell definition is
not a valid shop template; changing its extension does not convert it.
For relocated `.npc` definitions, verify animation dependency paths and their
resolution base on the target client before claiming appearance support.
Keep an existing proven directory layout as the diagnostic baseline; do not
attribute invisible actors to malformed shop data without an appearance test.
An invisible actor whose named interaction menu opens has already spawned;
prioritize `[field animation]`, its `.ani` and `[IMAGE]` resource chain rather
than reallocating IDs or changing coordinates. A menu's shop option does not
prove the shop opens or purchases work. Compare decoded animation content as
well as reference paths; identical `.ani` data does not prove that a relocated
NPC resolves it or that the client NPK supplies its images.
Reuse appearance through `.npc` `[small face]`, `[big face]`, `[popup face]`
and `[field animation]`; optional ambient/dialogue sound fields also belong
there. Keep the custom name and role/shop link independent of copied visuals.
NPC dialogue and favor records are not required merely to reuse appearance.

```text
[NPC]
	<NPC ID>
[type]
	`[etc shop]`
[sell info]
	...tabs or items...
[/sell info]
[message]
	`Shop dialogue`
```

Keep the target shop's existing `[type]` (`[etc shop]`, `[expert shop]`, …). Do not swap in an unchecked type.

## `[sell info]` (copy the target shop)

### One tab / many tabs

Each `[tab]` is a sibling with its own `[item list]`. Do not nest a second `[item list]` inside the first tab. Backtick text after `[tab]` is the tab label; `[item list]` order is display order.

```text
[sell info]
	[tab]
		`Consumables`
		[item list]
			1150 1151 1153
		[/item list]
	[/tab]
	[tab]
		`Other`
		[item list]
			10099377
		[/item list]
	[/tab]
[/sell info]
```

Snippets above are structural fragments, not full shops.

### Shop-level categories mixed with tabs

`[use category]` is shop-wide. **Each tab may use either category lists or a plain list.**

```text
[sell info]
	[use category]
		`basic job`
	[tab]
		`Rare`
		[category entry]
			[id]
				0
			[item list]
				101000282 101030306
			[/item list]
		[/category entry]
	[/tab]
	[tab]
		`Consumables`
		[item list]
			10088618
		[/item list]
	[/tab]
[/sell info]
```

`[category entry] [id]` is a class ID for the current `[use category]`, not a shop ID or item ID. A21 `basic job` IDs include `0,1,5,3,4,2,6,7,8,11,10,9,12,13`. Preserve the category numbering.

PVF also has `job`, `expert job`, `expert job non filter`, `pvp job`. Do not reuse `basic job` IDs on other categories.

### No tabs

- `StackableShop1.shp`: `[item list]` directly under `[sell info]`.
- `AbelroExpert.shp` (`[expert shop]`): `[category entry]` directly under `[sell info]`, plus `[use toggle]` / `[expert job level]`.

Reference `PvfLib.ItemShopFile` **does not extract items** from either. This is
a tool-index limitation, not proof that the shop is invalid at runtime. Keep
that NPC's existing shape; do not copy expert-job toggles onto a normal shop.

### Daily rotation

Daily rotation uses `[one a day start time]` / `[one a day item]`. Preserve the existing shape and verify the rotation in-game.

## Write rules

- `[item list]`: item IDs only. Space, tab, or newline are equivalent; newlines do not group in-game. No commas.
- Do not put price or effects in `.shp`. Before listing, check ID, price, level, bind, expiry.
- Pair `[tab]`, `[category entry]`, `[item list]`, `[sell info]`. Category goods go in that `[category entry]`'s `[item list]`; plain goods go in the tab's `[item list]`.
- Negatives (`-1` `-2`) are not item IDs.
- Resolve item IDs through `stackable/stackable.lst` or `equipment/equipment.lst`. Digit count is not the type.

## Tool-index limits

`ItemShopFile.cs` only reads `[item list]` **directly inside** a `[tab]` under `[sell info]`:

- no recursion into `[category entry]`
- no tabless shops

GM/tool shop indexes cannot prove category tabs or tabless shops. Verify category UI and purchase on the target client.

## Read-only close

1. `npc.lst` → `.npc` → `[role]` shop entry → shop ID
2. `itemshop.lst` → `.shp`
3. Check `[NPC]`, `[type]`, `[message]`, `[sell info]` shape
4. Collect every positive item ID; resolve each registry; read `[name]`, `[price]`, `[value]`, `[need material]`, and expiry. For medal sales, resolve the actual sale system before assigning a price field. Route CERA sales to cera-shop.
5. List secret shop, daily rotation, expert job, and log-only entries separately; they are not normal sale facts

## Write and verify

| Goal | Edit |
| --- | --- |
| List / unlist / retab | `.shp` `[item list]` / `[tab]` |
| Gold buy price | item `[price]` (plain stackables fall back to `[value]`) |
| Material exchange | item `[need material]`; resolve material IDs in stackable |
| CERA shop | Follow [cera-shop](../cera-shop/SKILL.md) |
| Medals / other currencies | Trace the target client's and server's handler first; do not infer a price path from `[medal]` alone |

In-game: find the NPC → open shop → check tabs / job categories / order → check
price → buy or exchange → check Chinese text. A partial tool index does not
override the actual definitions or observed UI. A client/server transaction
conflict needs investigation, not blanket preference for either side.
