---
name: cera-shop
description: >-
  Inspect or edit A21 CERA cash-shop listings in etc/cerashop.etc: product
  IDs, prices, pages, premium contracts, and catalog migration from another
  PVF. Use for 点券商城, 商城上架, CERA prices, or cerashop. For NPC .shp
  shops use npc-shop; for item definitions and package rewards also load items.
---

# A21 CERA shop

Read the shared workflow in [../SKILL.md](../SKILL.md). Item definitions and
package-opening behavior belong to [../items/SKILL.md](../items/SKILL.md).

## Product records and prices

`etc/cerashop.etc` maps **product ID → item ID**. The client buys by product ID;
resolve the item through `stackable/stackable.lst` or `equipment/equipment.lst`.
Changing an item's `[cash]`, `[price]`, or name does not set the CERA product price.

Columns are **1-based**; a backtick string with spaces is one token.
Preserve all non-target columns.

| Section | Tokens per record | CERA price column | Other established columns |
| --- | --- | --- | --- |
| `[item]`, `[premium]`, `[creature]`, `[coin]`, `[material]`, `[recoveryitem]` | 9 | 6 | product 1, item 2, count 3, gold 4; medal column 5 is not implemented by this reader |
| `[visual]` | 8 | 6 | product 1, item 2, count 3, duration days 4 |
| `[package]`, `[regular package]`, `[community package]` | 11 | 5 | product 1, item 2, count 3; column 4 is not a gold price |
| `[avatar]` | 6 | 6 | product 1, item 2; column 3 selects a 1-based avatar offer tier |
| `[selectable character premium]` | 9 | 6 | product 1, item 2, duration days 4; catalog count is 1 |
| `[charac premium package]` | 9 | 3 | product 1, item 2, duration days 8; catalog count is 1 |

For avatars, the purchase path also reads the equipment's `[avatar type select]`:
each offer has 7 tokens, with duration at column 1 and CERA price at column 4.
A resolved positive offer price overrides the catalog price. Inspect both the
product's tier and `.equ` before changing an avatar price or duration.

`[buy only cera]` and `[buy only cera point]` contain **item IDs**, not product IDs.
Preserve payment restrictions unless included in the requested change.
Read the complete target section; malformed token counts must be resolved before writing.

## Pages and collisions

In A21, `[regular package]` is the daily-package page and `[package]` is the main
package page; character-premium entries use the limited/service UI.
Check placement and display order in-game after editing.

Moving a product: remove its source row and add one destination row using the
destination layout. Read both sections back. Scan product IDs across all catalog
sections: the server stores them in one dictionary and later parsed rows overwrite
earlier collisions. Allocate replacements only after checking the final catalog;
preserve item IDs, prices, and other fields unless explicitly included in the task.

## Catalog migration

1. Compare the target and import catalog sections, item registries, definitions,
   and any explicit price exceptions. Copy only the requested catalog scope.
2. Classify missing registrations, missing definition files, and conflicting
   ID-to-path mappings separately. A listed product alone does not establish support.
3. Follow every package's direct reward references and nested gift-box references
   using the actual `[package data]` / `[booster info]` layouts.
   Resolve stackable and equipment rewards; use a visited set for cycles. Report
   unresolved IDs and unsupported effects. Listing-only permission does not authorize
   importing extra definitions; include required additions in the agreed scope.
4. For migrated missing stackables, append new `stackable/stackable.lst` pairs at
   the bottom in import registration order. Keep original pairs and their order;
   check ID and path collisions. In editable text, use one ID/path pair per line.
5. A Type 1 binary PVF stores tokens rather than original text whitespace. The
   current reader/writer normalizes displayed spaces/newlines: append order and
   mappings survive, but one rendered line per pair cannot be guaranteed. Do not
   change the file data type just to force formatting; report this limitation.
6. Reopen the result: compare record counts, added definitions, registry prefix and
   appended pairs, product IDs, target rows, price exceptions, and non-target content.
   Check the change-workbook paths against the actual content changes and additions.

## Contracts and resources

An individual timed contract needs its product row, `.stk` registration/definition,
and `etc/premiumlist_new.etc` item-to-service/period mapping. A multi-token
`[cera package]` must grant each intended token, and each token needs its service
mapping. All-service contracts in `[charac premium package]` use a dedicated
purchase path. Verify the purchase and activated services after moving a contract.

Check icon, preview, field-image, and equipment resources in the client NPK.
In-game checks: page/order, displayed and deducted price, purchase limits, item
delivery, nested rewards, contract activation, and art.
