# 任务旧进度修复

返回 [数据库目录](README.md)；关联字段见 [表结构速查](tables.md)，修改前按 [手动维护流程](manual-workflow.md)备份和停服。
本页处理任务条件变化后保存的旧进度；SQL 模板只能由你执行。

## 定位与修改范围

- 按角色及任务 ID 查询 `character_active_quests`，核对任务类型、目标通道和新 PVF 要求。
- 明确重置目标还是保留已完成次数；旧要求需有可靠依据，不能从负显示反推。
- 保留 `slot`、`activation_id`、其他通道及无关任务；每日任务的领取周期另行排查。

## 击杀任务的剩余计数

`.qst` 的 `[hunt enemy] [int data]` 每组五列：
副本 ID、最低难度、目标编号、目标类型、要求数量。
`character_active_quests.trigger_value` 按位保存最多三个剩余次数，
每通道 9 位，移位为 `0`、`9`、`18`；每个通道可表示 `0～511`。
**剩余为 0 是目标完成，不是“从头开始”**；整体值为 0 表示目标全部完成，不代表已交任务或奖励已到账。

| 要算什么 | 公式 |
| --- | --- |
| 第 0 通道剩余 | `trigger_value & 511` |
| 第 1 通道剩余 | `(trigger_value >> 9) & 511` |
| 第 2 通道剩余 | `(trigger_value >> 18) & 511` |
| 普通目标显示已完成 | `PVF 要求数量 - 对应剩余次数` |
| 三通道完整打包 | `r0 + (r1 << 9) + (r2 << 18)` |
| 只替换一个通道 | `(旧值 & ~(511 << shift)) \| (新剩余 << shift)` |

只修一个目标时用通道替换，不能把整个值直接改成该目标数量。
只有确实要求重置全部目标时才完整重新打包；原本已完成的其他通道保持 0。
超出 `0～511` 的目标不能直接套模板，参考代码会钳制到范围内，不能用位移溢出代替解决。

### 为什么会显示 `-11/2`

例如新 PVF 要求为 2，旧数据仍保存对应通道剩余 13，界面按 `2 - 13` 计算出 `-11`。
这是新条件与旧剩余不匹配的一种情况，**不是数据库必然存了 -11**。
先解码确认目标通道和任务类型；这个示例不适用于所有收集、成就或特殊任务。

| 修复目标 | 新剩余如何取 |
| --- | --- |
| 将这个目标从头开始 | 设为新 PVF 要求；示例设为 2，显示 `0/2` |
| 保留已经完成的次数 | 先由可信旧要求及旧剩余算 `旧已完成 = 旧要求 - 旧剩余`，再取 `max(0, 新要求 - 旧已完成)` |
| 只修一个目标 | 替换对应通道，其他剩余次数不变 |

没有旧要求或可靠完成记录时，不能从负显示反推出已完成多少。
如果已完成次数已超过新要求，新剩余为 0 是保留进度的结果，应明确这会使目标完成。
不默认清零全部进度，也不删除任务或重置称号成就来掩盖显示问题。

### 查询模板（由你执行）

`@cid`、`@qid` 等参数需要在你的数据库工具中绑定，或替换为已核实的值。

```sql
SELECT character_id, slot, quest_id, activation_id, version, trigger_value,
       trigger_value & 511 AS remaining0,
       (trigger_value >> 9) & 511 AS remaining1,
       (trigger_value >> 18) & 511 AS remaining2
FROM character_active_quests
WHERE character_id = @cid AND quest_id = @qid;
```

### 单通道更新模板（仅由你执行）

`@shift` 只能是 0、9、18，`@remaining` 是已经确认的 `0～511` 新剩余；
`@activation`、`@old_version`、`@old_trigger` 取自上述查询。
本例同时匹配旧版本和旧值，更新时增加 `version`，与参考代码的并发保护一致。

```sql
BEGIN IMMEDIATE;

UPDATE character_active_quests
SET trigger_value = (trigger_value & ~(511 << @shift))
                    | (@remaining << @shift),
    version = version + 1
WHERE character_id = @cid AND quest_id = @qid
  AND activation_id = @activation
  AND version = @old_version AND trigger_value = @old_trigger
  AND @shift IN (0, 9, 18) AND @remaining BETWEEN 0 AND 511;

SELECT changes() AS affected_rows;

SELECT slot, quest_id, activation_id, version, trigger_value
FROM character_active_quests
WHERE character_id = @cid AND quest_id = @qid;
```

更新后保持事务开启：`affected_rows` 必须为 1，新版本应为旧版本加 1，
其他通道与 `activation_id` 应保持原样。确认后由你执行 `COMMIT;`；
结果不符则执行 `ROLLBACK;`，不得在未核对时提交。

## 收集任务 `[seeking]`

参考实现的第一个通道保存所有所需物品的缺口总和：
`Σ max(0, 要求数量 - 实际持有数量)`，不是已收集数，也不是每种材料占一个通道。
背包变化/客户端重算可能更新缺口，但改 PVF 不直接迁移旧行。
手动修复前按实际持有数量重算，结果仍须满足通道容量，并保留其他通道和激活标识。
不要把击杀任务的“设为要求数量”方案直接套到收集任务。

## 修改后验证

核对影响行数、新版本、激活标识及各通道；提交后重新启动服务端并加载对应 PVF。
登录检查显示，再完成一次目标，确认计数变化和正常交任务；不以数据库新值代替游戏验收。

## 代码核对入口

- [QuestTrigger.cs](../../ServerS4A21/Server/DfoServer/Game/Quests/QuestTrigger.cs)：9 位计数读取与替换。
- [QuestRepository.cs](../../ServerS4A21/Server/DfoServer/Game/Quests/QuestRepository.cs)：版本与激活标识。
- [QuestProgressReducer.cs](../../ServerS4A21/Server/DfoServer/Game/Quests/QuestProgressReducer.cs)：收集缺口重算。
