---
name: gold-drop
description: >-
  Inspect or edit S4A21 次元彼端 dungeon gold drop probability, amount, variance, and
  clear-card gold multipliers. Use for gold drop probability, quantity, multipliers, or
  ItemDropInfo gold tables. Covers reference ServerS4A21 interpretation; NPC
  buy/sell prices belong to items, and CERA prices belong to cera-shop.
---

# A21 gold drops

Scope: S4A21 次元彼端. Apply [../SKILL.md](../SKILL.md) for target/no-target
handling and read-only references. Formulas here describe reference ServerS4A21,
not effects inferred solely from PVF tag names.

## Edit boundary and read-back

Choose monster, object or clear-card gold before selecting the table. Resolve
relevant dungeon/monster references when narrowing scope; table level columns
are levels, not registry IDs. Change only the authorized amount/probability
fields and level groups; preserve item rates and unrelated bonuses. Common
base/variance affects several branches. Reopen and check exact changed cells,
probability scale, rounding and overflow, then verify frequency and amount
separately under controlled in-game conditions.

Use [../SKILL.md](../SKILL.md) for authorization, output, and read-back rules.
First distinguish monster drops, breakable-object drops, clear-card gold, and
NPC sale/recycle prices. Amount, appearance probability, and multipliers are
different controls. The formulas below apply to reference ServerS4A21.

## Configuration

| PVF path / tag | Meaning in reference ServerS4A21 |
| --- | --- |
| `etc/itemdropinfo_common.etc` / `[gold drop ref table]` | triples: monster level, base gold, variance percent; levels 1–200 |
| `etc/itemdropinfo_monseter.etc` / `[drop prob]` | 7-value rows; columns 1–2 are level min/max, column 3 is gold rate; preserve columns 4–7 and the spelling `monseter` |
| same file / `[monster type drop bonusrate]` | 5 categories × 4 monster types; first category's 4 values multiply gold probability |
| `etc/itemdropinfo_object.etc` / `[drop prob]` and bonus matrices | object gold probability is category 0; difficulty and actor-type bonuses are applied; reference ServerS4A21 uses object actor index 0 |
| `etc/itemdropinfo_clearreward.etc` / `[dungeon difficulty gold drop bonusrate]` | clear-card gold amount multipliers by difficulty index |
| same file / `[party member drop bonusrate]` | clear-card party-size multipliers; per-member calculation also divides by member count |

Gold base/variance from the common table are shared by monster drops, random
object drops, and ordinary clear-card gold. They are indexed by monster/object
level or dungeon basis level according to the caller. Changing that table has
effects beyond monster drops.

## Probability and quantity

For monster gold, reference ServerS4A21 computes:

```text
typeRate = int(baseRate × monsterTypeBonus)
rate = min(int(typeRate × difficultyBonus), 10000)
drop if rate > LCG.Next(10000)
delta = random integer in [-variancePercent, +variancePercent]
goldVariance = integer(delta × baseGold / 100)
amount = max(1, int((baseGold + goldVariance) × difficultyBonus))
```

The probability scale is 10,000 (750 corresponds to a nominal 7.5%). Integer
division follows C# truncation. The reference ServerS4A21 monster difficulty bonuses are
**hardcoded** as `1, 1.2, 1.4, 1.6, 1.8` for indices 0–4, affecting both rate
and amount. `MonsterDropConfig` does not read the monster file's difficulty or
party bonus matrices for this path. `[gold quantity]`, `[gold volume]`, and DGN
`[gold drop prob]` are not applied in this monster-gold path.

Object gold uses its own scaled probability from the object table, capped at
10000, and checks `LCG.Next(10001) < rate`; its nominal probability is rate/10001.
It shares common-table
base/variance; its amount generator does not apply the monster hardcoded
difficulty multiplier and clamps nonpositive amounts to 1.

Ordinary free clear-card gold uses:

```text
weightedKills = normalKills + 2 × championKills + 4 × bossKills
memberGold = nonnegative int(
  baseGold × 0.175 × weightedKills / 2 × difficultyRate × partyRate
  × (1 + rankBonusRate) / partyMembers)
then apply common-table variance; clamp to nonnegative int
```

No weighted kills yields 0. Negative clear-card variance results clamp to 0;
monster gold clamps to 1. `[gold card create rate]` controls paid item-card creation.
Tournament DGN `[tournament clear reward gold rate]` is a direct multiplier in
the tournament path (16 means ×16). Pickup applies equipped gold bonuses and
carry limits, so distinguish generated gold from credited gold.

The common table's third column is a percentage, not a second gold amount.
With a positive base and variance at least 100, rolls at or below -100 percent
produce nonpositive raw gold and trigger the monster minimum of 1. Increasing
the base does not remove this cause; use variance 0 for a fixed amount before
the difficulty multiplier, or a range below 100 for bounded percentage variation.

The base-gold column is parsed as Int32; its positive field ceiling is
2,147,483,647, not a safe end-to-end drop limit. Monster variance multiplies
`delta * baseGold` in Int32 before division, and pickup likewise multiplies
`dropGold * equippedBonusPercent` in Int32 before division. Bound those products,
the pre-difficulty sum, and the floating-point difficulty result separately.
Ground gold packets use a UInt32 amount; the clear-card UInt16 display limit
does not apply to them. Pickup credits only available carrying capacity, using
the greater of the PVF `etc/(r)goldlimitbylevel.etc` level limit and the saved
character limit. An actual maximum therefore depends on variance, difficulty,
equipment bonuses, and the character's current balance and carrying limit.

## How to change and verify

- For an amount multiplier, multiply only the requested level rows' base-gold
  column using an explicit rounding choice; preserve variance unless requested.
  For probability, edit the relevant gold-rate column or bonus entry;
  preserve all item-rate columns and table shape.
- Read the target tables. Check row counts, level uniqueness/ranges, bonus indices,
  integer overflow, caps, and extreme variance.
- Read the changed table back, compare all non-target values/content, and check
  actual change-workbook paths. Test monster gold frequency and amounts separately
  from object gold and clear cards on matching client/server PVFs. Keep level,
  difficulty, monster type, party size, rank, and equipment bonuses controlled.
