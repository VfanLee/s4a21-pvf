---
name: cera-shop
description: >-
  Inspect or edit S4A21 次元彼端 CERA cash-shop listings in etc/cerashop.etc: product
  IDs, prices, pages, premium contracts, and catalog migration from another
  PVF. Use for cash-shop catalogs, product listings, CERA prices, or cerashop. For NPC .shp
  shops use npc-shop; for item definitions and package rewards also load items.
---

# A21 CERA shop

Scope: S4A21 次元彼端. Apply [../SKILL.md](../SKILL.md) for the designated
target, no-target answers and read-only reference projects.

## Edit boundary and read-back

Close product ID → item ID → item registry/definition, then any nested rewards
or premium-service mappings. Listing-only work preserves item definitions;
price/page work preserves unrelated columns and payment restrictions. A whole
UI category can span multiple sections. Record the complete authorized scope.
Read back token counts, product collisions, auxiliary references and unchanged
sections; verify page/order, deducted payment and delivery separately in-game.

Locate this A21 catalog at `etc/cerashop.etc`. External bookmarks naming
`etc/newcashshop.etc` or `etc/newcashshop_restrict.etc` do not establish those
files as this server's catalog or restriction source; inspect the actual catalog
sections and consumer rather than creating or substituting another-version files.

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
each offer starts with 7 numeric tokens, with duration at column 1 and CERA price
at column 4. Socket descriptors can follow the numeric offers; preserve them.
A resolved positive offer price overrides the catalog price. Inspect both the
product's tier and `.equ` before changing an avatar price or duration.

`[buy only cera]` and `[buy only cera point]` contain **item IDs**, not product IDs.
Preserve payment restrictions unless included in the requested change.
Read the complete target section; malformed token counts must be resolved before writing.

For this A21 catalog, `[gift disallowance]` lists product IDs; `[btn control]`
uses four-token records with the product ID in column 1. `[buy restrict item]`
also uses four-token records, but column 1 is the restriction-entry ID and
column 2 is the product ID. Preserve columns 3–4 unless their meaning and change
are separately established; never match its entry ID as a product reference.

## Pages and collisions

For `etc/eventcharacter/cerashop.etc`, distinguish archived catalog content
from the active purchase source. Reference ServerS4A21's CERA catalog loads
`etc/cerashop.etc` (or its documented local fallback), with no loader reference
to the eventcharacter catalog. Editing only the latter does not establish
server purchase support; its client activation conditions remain unverified.
Standard `[material]` entries have nine tokens, independent of line breaks.
Append complete records before `[/material]`, preserving existing records;
keep trailing flags unless their meaning has been verified. Resolve items
and check product IDs across the active catalog before proposing an addition.

For this A21 catalog, the homepage recommendation list `[hotnew sell ipg]`
contains product-ID/presentation-value pairs. Check it separately from product
records when investigating a missing homepage offer. Restore the complete source
pair when recovering a filtered offer; keep its presentation value and order.

In A21, `[regular package]` is the daily-package page and `[package]` is the main
package page; character-premium entries use the limited/service UI.
Check placement and display order in-game after editing.

For this A21 client's avatar page, `old product` follows catalog record order and
`new product` shows its reverse; changing product IDs alone does not reverse the
visible sequence. Write the desired `new product` sequence backwards, including
the eight slots within each set. Keep product-to-item mappings intact for row-only
repairs. Recheck the actual selected mode before claiming first-page placement.

Validate sorting by extracting the per-job records in physical order and comparing
their reversed item-ID sequence with the complete intended display sequence.
When IDs correlate with row order, a screenshot alone cannot identify the sort key;
change only one ordering variable and compare both `new product` and `old product`.
Archive read-back proves the configured sequence, not a successful in-game check.

Moving a product: remove its source row and add one destination row using the
destination layout. Read both sections back. Scan product IDs across all catalog
sections: the server stores them in one dictionary and later parsed rows overwrite
earlier collisions. Allocate replacements only after checking the final catalog;
preserve item IDs, prices, and other fields unless explicitly included in the task.

## Rare avatars and vouchers

Resolve avatar IDs through `equipment/equipment.lst`; scan both `avatar/` and
`at_avatar/` paths. Rare avatars use `[grade] 3`; `[rarity]` is a separate field.
Check each job and color for the eight equipment slots: hat, hair, face, breast,
coat, pants, waist, and shoes. Skin and aura are separate slots. Keep distinct
wing/shoulder alternatives; a second item for one slot can be a valid appearance.

Resolve actual definitions and usable-job restrictions. Compare animation job,
variation, layer/script, selectable abilities and set-effect indexes before
classifying duplicates. GM/debug-only items or unresolved definitions do not
establish a sellable set; a localization-placeholder name alone is not invalid.
Rare clones have `[item category]` `clear avatar`; check the grade and eight slots
to avoid listing advanced clones as rare avatars.

For cross-slot color grouping, compare the named palette and the actual appearance;
the second `[variation]` value is not a universal color index across equipment slots.
The same named palette can use different numeric indexes in different slots.
Same-name clones can differ in minimum level or offers; compare these fields before
deduplicating them. When grouping is requested, keep each eight-slot set together
and use the user's display order; do not impose a fixed clone-first policy.

For release-date ordering, use each set's first Chinese-server release, preferably
from official sources. Equipment IDs and file timestamps are not release dates.
Record source precision; keep normal sets without a confirmed Chinese release
after dated sets instead of inventing dates.

For integer-only changes to a Type 1 catalog section, splice its type-0 numeric
tokens between the original section markers. Preserve all other raw tokens and
string-reference offsets; verify both the untouched chunks and neighboring payloads
in the rewritten chunk after reopening the archive.

For reference ServerS4A21, voucher payment (`paymentMode=1`) with grade 3 and offer
tier 3 consumes one rare-avatar voucher (`2681594`) and sets the CERA charge to 0.
Confirm the third `[avatar type select]` offer has duration 0. The catalog row is:

```text
productId itemId 3 0 0 -1
```

Retain the `.equ` prices and other offer data for listing-only changes. Voucher
payment is a purchase mode; positive offer prices still apply to CERA purchases.
Verify voucher deduction, permanent delivery, preview, and display order using
the actual client's selected sort mode before confirming first-page placement.

Separate catalog presence, definition support, and in-game visibility in reports.
After appending rare avatars, compare product-ID allocation with existing avatar
groups for each job; global uniqueness alone does not establish client visibility.
If only a subset appears, check the active catalog, job/category filtering, and
hide configuration before adding duplicate products. Verify each job's visible
pages with the actual client before calling the listing complete.

## Catalog migration

An authorized UI category restoration can span several catalog sections; do not
equate the whole category with `[item]` alone. Establish the complete section
boundary first. Restore scoped auxiliary records by their actual product/item
reference columns, preserving every unrelated record and its order. Compare
all out-of-scope catalog token spans and all other file payloads with the target
before publication. Reuse unchanged target records when their resolved content
already matches the reference; an existing matching record does not prove
client visibility, so do not create a duplicate to address a missing display.

Report the number of records checked/restored separately from records that
actually changed. Identify added products, changed fields and auxiliary rules;
when the reported missing product already matched the reference, state that
catalog restoration leaves its visibility issue unresolved until game validation.

For listing-only migrations bounded by the original target, preserve target item
definitions and resource references. Validate the reward graph that will actually
run: the target's graph when no definitions are imported. Include
`[package data selection]`, `[package data with usable period]`, booster selection,
and `[RANDOMBOX]`; checking only fixed rewards misses nested random boxes.
Compare the import graph to record content differences, but do not filter a
target-supported product solely because an unchanged import definition references
extra rewards that will not be copied. Validate import-only dependencies when
their definitions are actually included in the authorized migration.

Type 1 string offsets belong to one archive. When moving a complete catalog,
relocate every string-bearing token into the target string table while retaining
its token type and validating the resolved text. Do not copy source offsets raw.
Keep the `-1` aggregate row in `[selectable character premium]`; it is not a
positive purchasable product ID and must not receive an allocated replacement.

When repairing duplicate positive product IDs, check auxiliary references before
renumbering. A reference to an ambiguous original ID cannot identify which
duplicate it intended. For filtered products, remove complete auxiliary records
only after identifying the product-reference column; preserve unrelated integer
fields and pre-existing non-product placeholders.

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
   a compatible Type 1 reader/writer normalizes displayed spaces/newlines: append order and
   mappings survive, but one rendered line per pair cannot be guaranteed. Do not
   change the file data type just to force formatting; report this limitation.
6. Reopen the result: compare record counts, added definitions, registry prefix and
   appended pairs, product IDs, target rows, price exceptions, and non-target content.
   Check the change-workbook paths against the actual content changes and additions.

For partial avatar migrations, protect the retained catalog rows and their actual
equipment definitions/registry mappings. Follow reward dependencies even when
the product's own definition already exists. Check source-internal duplicate
product IDs too: copying a reference catalog can reproduce purchases that resolve
to a different item. Record any authorized replacement IDs separately from
item/content/price synchronization. Normalize slash direction and case before
reporting ID-to-path conflicts; do not duplicate equivalent paths.

Classify resource failures by tag. `[avatar package preview info]` points to a
package display image; a missing preview does not establish that reward icons
or worn avatars are missing. Report each category separately. Keep any exception
to resource completeness explicit rather than dropping products silently.

When pruning packages against an original support boundary, distinguish the current
edit target from the comparison archive. Resolve each package and its complete
nested reward graph through the comparison archive's registries and actual files;
an ID match with a different path requires review. Remove the whole unsupported
catalog record rather than silently reducing its rewards. Compare retained items'
image/animation references with the comparison definitions before claiming no new
client assets are required; definition support alone does not prove in-game art.
Unlisting does not require deleting definitions or registrations that other systems
may reference. Preserve retained rows, order, prices, and all other catalog sections.

For completeness audits, enumerate the target's registered package definitions
and compare distinct item IDs with every relevant shop section. Filtering an import
catalog by target support yields only their intersection, not all supported target
packages. Distinguish missing original listings from definitions that were never
listed in either catalog. Inspect package type and reward graph, not names alone;
report activities, placeholders, nested reward boxes, and unresolved rewards
separately. A complete definition graph is a support candidate, not proof of valid
client art, purchase behavior, or an established CERA price.

When listing previously unlisted original packages, CERA prices come from the
catalog, not the item's NPC `[price]`. If no original listing exists, apply the
user's pricing rule and record the existing comparison product. Compare actual
package kinds; the generic `stackable/cash/` directory is not a shared series.
Do not discard otherwise supported packages merely because their names are
localization placeholders; follow the item skill's name checks.

The package catalog's final field is the job filter. Existing common products
can use `-1`; copy the verified common-product layout instead of duplicating one
unrestricted item across invented job codes. For missing or misspelled item job
fields, inspect reward avatars' usable-job restrictions before assigning a page;
conflicting restrictions require review rather than a guessed job.

## Contracts and resources

An individual timed contract needs its product row, `.stk` registration/definition,
and `etc/premiumlist_new.etc` item-to-service/period mapping. A multi-token
`[cera package]` must grant each intended token, and each token needs its service
mapping. All-service contracts in `[charac premium package]` use a dedicated
purchase path. Verify the purchase and activated services after moving a contract.

Check icon, preview, field-image, and equipment resources in the client NPK.
In-game checks: page/order, displayed and deducted price, purchase limits, item
delivery, nested rewards, contract activation, and art.
