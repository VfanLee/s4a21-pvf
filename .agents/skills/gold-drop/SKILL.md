---
name: gold-drop
description: >-
  Inspect or edit A21 dungeon gold drop probability, amount, variance, and
  clear-card gold multipliers. Use for 金币爆率, 金币掉落数量, 金币倍率, or
  ItemDropInfo gold tables. Covers current ServerS4A21 interpretation; NPC
  buy/sell prices belong to items, and CERA prices belong to cera-shop.
---

# A21 gold drops

Use [../SKILL.md](../SKILL.md) for authorization, output, and read-back rules.
First distinguish monster drops, breakable-object drops, clear-card gold, and
NPC sale/recycle prices. Amount, appearance probability, and multipliers are
different controls. The formulas below apply to this project's A21 server.

## Configuration

| PVF path / tag | Meaning in the current server |
| --- | --- |
| `etc/itemdropinfo_common.etc` / `[gold drop ref table]` | triples: monster level, base gold, variance percent; levels 1–200 |
| `etc/itemdropinfo_monseter.etc` / `[drop prob]` | 7-value rows; columns 1–2 are level min/max, column 3 is gold rate; preserve columns 4–7 and the spelling `monseter` |
| same file / `[monster type drop bonusrate]` | 5 categories × 4 monster types; first category's 4 values multiply gold probability |
| `etc/itemdropinfo_object.etc` / `[drop prob]` and bonus matrices | object gold probability is category 0; difficulty and actor-type bonuses are applied; current object actor index is 0 |
| `etc/itemdropinfo_clearreward.etc` / `[dungeon difficulty gold drop bonusrate]` | clear-card gold amount multipliers by difficulty index |
| same file / `[party member drop bonusrate]` | clear-card party-size multipliers; per-member calculation also divides by member count |

Gold base/variance from the common table are shared by monster drops, random
object drops, and ordinary clear-card gold. They are indexed by monster/object
level or dungeon basis level according to the caller. Changing that table has
effects beyond monster drops.

## Probability and quantity

For monster gold, the current code computes:

```text
typeRate = int(baseRate × monsterTypeBonus)
rate = min(int(typeRate × difficultyBonus), 10000)
drop if rate > LCG.Next(10000)
delta = random integer in [-variancePercent, +variancePercent]
goldVariance = integer(delta × baseGold / 100)
amount = max(1, int((baseGold + goldVariance) × difficultyBonus))
```

The probability scale is 10,000 (750 corresponds to a nominal 7.5%). Integer
division follows C# truncation. The current monster difficulty bonuses are
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
