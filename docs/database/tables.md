# 数据库表结构速查

返回 [数据库目录](README.md)。表字段依据参考 ServerS4A21 的 SQLite 结构，修改前核对实际数据库。

## 已核实的表

| 表 | 定位字段 | 关键数据 | 与 PVF 的关系 |
| --- | --- | --- | --- |
| `character_active_quests` | `character_id`、`quest_id`；主键为角色及 `slot` | `trigger_value`、`version`、`activation_id` | 已接任务进度；改 `.qst` 要求不会自动更新旧行 |
| `character_quest_completions` | `character_id`、`quest_id` | `completion_value` | 已交任务/问答分支值；普通接取判断非零为已完成，不是杀怪次数 |
| `character_achievements` | `character_id`、`achievement_id` | `p1`、`p2`、`p3`、`p4`、`sort_order` | 成就/称号任务剩余计数，不等于普通已接任务进度 |
| `character_titlebook_items` | `character_id`、`category`、`slot_index` | `item_core`、`updated_at` | 实际称号实例；参考结构的 `item_core` 为 99 字节 BLOB，不手工编造 |

每日任务的完成判断还读取领取周期，不能只改 `completion_value` 认定已经重置；
[普通任务修复模板](quest-progress.md)不覆盖每日领取周期。

任务 ID、前置任务 ID、称号成就 ID、称号物品 ID 不能混用。
先从当次 PVF 的 `n_quest/quest.lst`、任务定义和 `etc/titlebook.etc` 闭合关联。
没有实际 PVF 或旧配置时，不能仅凭任务名、界面数字推断旧条件或具体编号。

## 代码核对入口

[表定义 item_schema.sql](../../ServerS4A21/Server/DfoServer/Sqlite/item_schema.sql)。

当前只记录已调查的表；新增表按定位键、关键字段和关联用途补充，不猜测未知字段。
