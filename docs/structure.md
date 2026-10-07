# PVF 结构

对应 [`AGENTS.md`](../AGENTS.md)。本仓库是 skill 包，不是解包后的脚本树。

PVF 内部路径可复用，本机绝对路径和工具位置由这次指定的目标与执行环境确定。
应用已记录的规则不要求携带旧电脑目录、服务端源码或历史附件；目标实际值和登记关系
仍须从本次提供的 PVF 读取，不能套用旧包内容。

## 目标与只读参考

- 有 PVF/解包目录：以当次提供或明确指定的目标为准，不自动挑选其他包或基线。
- 无目标：只按技能中已记录的知识回答，说明未核实实际配置；缺少记录就明确说不确定、无法提供可靠结论。
- `ServerS4A21`、`S4A21ClientPatch`、`S4A21GmTool` 默认只读，用于排查字段与读取行为；服务端问题由用户自行修改。
- `custom-pvf` 是用户的学习、导入和分享资料，AI 仅按需只读，不写入、整理、改名、删除或自动套用。
- 数据库仅用户可修改。AI 可参考代码解释字段、提供手动示例，不能写数据或代跑修复脚本。
- 技能、目标内容与代码冲突时先调查并说明；方案需要变化时，与用户决定后再改。

查询或修改中确认了新的可复用事实，交付前同一轮写入对应技能/AGENTS.md 和中文页。
内容精炼，只写已确认的操作规则和字段含义，保留必要的版本/运行环境范围。
不写来源叙述、历史案例、参考包名称、一次性数值或不确定的解释，不依赖旧任务附件。
新主题补齐独立技能、中文页及两边入口；没有变化的知识不重复改写。

## 打包和解包

| 东西 | 是什么 |
| --- | --- |
| `Script.pvf` | 打包后的档案，**不能当文本改**；只有在明确授权后，才能用 PVF 解析/写入工具重写 |
| 解包脚本树 | 如果用户提供了，才能直接改 `.lst` / `.npc` / `.shp` / `.stk` / `.equ` 等 |
| 用户明确指定的原版 / 基线 PVF | 对照锚点；不能只凭文件名就把某个 PVF 当成基线 |

改完要让客户端和服务端加载**同一份** PVF。服务端会保留进程级物品缓存，已确认可靠的清理方式是重启；只有部署环境已验证支持重载时，才可以使用重载。

先确认目标是解包目录、只查询的已打包 `Script.pvf`，还是已经授权重写的已打包 PVF。重写打包 PVF 时：必须用 PVF 解析/写入工具，先写临时文件并回读验证，最后才原子替换已授权目标。未解包时不要臆造路径。

## 副本难度与入口

全局与独立难度表、原版对比、旧副本入口的检查方法见 [副本难度文档](skills/dungeon-difficulty.md)。

## `.lst`：ID 从哪来

`.lst` 的一行是 `数字 ID → 该目录下的相对路径`。**这个映射才是 ID。**

改 `[name]`、`[explain]`、文件名，都不会生成新 ID。新道具必须同时有：空闲 ID、`.lst` 行、定义文件。

`itemname.lst` 这类名称表**不是**路径表。用户给中文名：先搜名称再解析 ID。用户给数字：走对应 `.lst`。

同一个数字可以同时出现在多个 registry 里（例如商店 ID 和物品 ID 撞号）。按当前任务选对表。

`.lst` 行示例（示意，不是完整文件）：

```text
3	`Kanna.npc`
84	`84_Kanna.shp`
3037	`material/cubepiece_clear.stk`
```

反引号里的路径相对该 `.lst` 所在目录。

## 顶层目录

解包后常见结构：

```text
<解包根>/
├── npc/           npc.lst → .npc
├── itemshop/      itemshop.lst → .shp
├── stackable/     stackable.lst → .stk
├── equipment/     equipment.lst → .equ
├── n_quest/       quest.lst → .qst
├── monster/       monster.lst → .mob
├── dungeon/       dungeon.lst → .dgn
├── map/           map.lst → .map
├── skill/         skilllist.lst → 各职业技能
├── character/     character.lst
├── town/          town.lst
├── worldmap/      worldmap.lst
└── etc/           .etc / 表（没有统一 ID 表）
```

| 目录 | 登记表 | 定义文件 | 内容 |
| --- | --- | --- | --- |
| `npc/` | `npc/npc.lst` | `.npc` | NPC |
| `itemshop/` | `itemshop/itemshop.lst` | `.shp` | NPC 商店上架 |
| `stackable/` | `stackable/stackable.lst` | `.stk` | 可堆叠：材料、药剂、礼包、徽章等 |
| `equipment/` | `equipment/equipment.lst` | `.equ` | 装备、称号、装扮、宠物 |
| `n_quest/` | `n_quest/quest.lst` | `.qst` | 任务 |
| `monster/` | `monster/monster.lst` | `.mob` | 怪物 |
| `dungeon/` | `dungeon/dungeon.lst` | `.dgn` | 副本 |
| `map/` | `map/map.lst` | `.map` | 地图 |
| `skill/` | `skill/skilllist.lst`（再进职业表） | 技能脚本 | 技能 |
| `character/` | `character/character.lst` | 角色脚本 | 职业/成长 |
| `town/` | `town/town.lst` | 城镇脚本 | 城镇 |
| `worldmap/` | `worldmap/worldmap.lst` | — | 世界地图 |
| `etc/` | 无统一 ID 表 | `.etc` / 表 | 杂项配置 |

## 三种编号不要混

NPC ID ≠ 商店 ID ≠ 物品 ID。

正向闭合（以这个为准）：

```text
npc/npc.lst          → NPC ID  → npc/*.npc
.npc 的 [role] 里 [item shop] 后面的数字 = 商店 ID
itemshop/itemshop.lst → 商店 ID → itemshop/*.shp
.shp 的 [NPC] 后面的数字 = NPC ID（回指，不能替代上面那条链）
```

NPC 编号、角色绑定的商店编号及上架物品编号分别读取，文件名不证明编号。

## 职责分层

- `.shp` 只负责**上架**（`[item list]` 里写物品 ID）
- NPC 金币买价、回收、绑定、效果、期限在道具的 `.stk` / `.equ`；点券商城的价格与页面在 `etc/cerashop.etc`
- A21 商店的**常见**结构是 `[sell info]` → `[tab]` → `[item list]`，可再加 `[use category]` / `[category entry]`；也存在无页签、仅职业分类、每日轮换商店，必须沿用目标结构
- 不要把其他版本的 `[sell item]`、`[tab name]` 套到本 PVF

## 运行时以谁为准

买卖价格、期限、礼包发放、点券购买及掉落算法描述参考 ServerS4A21/客户端的行为，
不是只凭 PVF 标签推断的功能。目标实际值和游戏验证结果分开报告。

Script.pvf 里的图标路径**不证明**客户端 NPK 里真有这张图。

## 按场景打开 skill

| 要改什么 | 中文 | 模型 |
| --- | --- | --- |
| 脚本路径、职业与技能树定位 | [定位索引](skills/script-navigation.md) | [script navigation](../.agents/skills/references/script-navigation.md) |
| NPC 放置、对话说话人与立绘、商店页签、上架、分类、`.shp` | [skills/npc-shop.md](skills/npc-shop.md) | `.agents/skills/npc-shop/SKILL.md` |
| 点券商城上架、售价、页面、契约、商品迁移 | [skills/cera-shop.md](skills/cera-shop.md) | `.agents/skills/cera-shop/SKILL.md` |
| 道具定义、NPC 道具价格、期限、礼包与任务奖励、`.stk` / `.equ` | [skills/items.md](skills/items.md) | `.agents/skills/items/SKILL.md` |
| 金币掉落概率、数量、浮动、通关倍率 | [skills/gold-drop.md](skills/gold-drop.md) | `.agents/skills/gold-drop/SKILL.md` |

NPC 商店与道具一起改，先改定义再改 `.shp`；点券商城与奖励一起改，同时加载商城和道具技能，
先核对定义/奖励再改商品表。只有点券商城任务时，不需要 NPC 商店技能。

技能、副本、怪物、NUT、其他掉落没有独立文档时，在有登记表的情况下走对应 `.lst` 读原文；
`etc/` 全局表没有统一登记表。所有任务都遵守下面的硬规则。

## 硬规则

- 默认只读。没有明确授权：不写 PVF、不改客户端 ImagePacks2/NPK、不覆盖原版包。
- 数字 ID 不是事实，必须经正确 `.lst` 解析后再读文件。
- 新道具必须同时有空闲 ID、`.lst` 行、定义文件。改名字不会分配 ID。
- 新增块或新文件：先对照同目录、同扩展名、同用途的 2–3 个近邻。不要凭标签名想象格式。
- 保留标签、反引号、数值顺序和成对 `[/...]`；可编辑文本保留空白。
  Type 1 二进制 PVF 的回读排版可能规范化，需比较解析后的内容与顺序并说明限制。
  不要把文档示例粘成完整文件。
- `.shp` 只上架。价格、绑定、效果、期限在道具文件。
- `[explain]` 是说明文字，不等于效果。图标路径不证明客户端资源存在。
- 写完必须读回。行为结论要说明实机怎么测。客户端和服务端加载同一份新 PVF；
  重启服务端是已确认的缓存清理方式，重载只在部署环境已验证支持时使用。

输出命名与持续改动清单的约定集中在 [skills/README.md](skills/README.md#本项目输出与改动清单)。

## 流程

```text
只读闭合 →（授权后）最小改动 → 读回 → 打包匹配的客户端/服务端 PVF → 实机验收
```

1. 确认改的是 NPC / 道具 / 商店 / 其他，以及这次能不能写。
2. 用名称或 ID 走对应 `.lst`，读目标文件，不要靠文件名猜。
3. 列出路径、字段、旧值、新值，以及商店、配方、任务、礼包等依赖。
4. 获授权后再改，只改计划内字段。
5. 读回改动文件和相关 `.lst`。
6. 记录：改了什么、没改什么、打包注意、实机怎么验、旧道具实例会不会自动刷新。

## 深渊开启流程排查（A21）

先闭合 `n_quest/quest.lst → 已登记的任务 .qst → worldmap/*.wdm 的 [hell quest]`。比较登记时统一路径分隔符；包内有任务文件但登记表没有对应 ID，不能据此认定任务可接取。

任务需要一起核对等级、类型、目标数据、前置任务、奖励类型和奖励数据。对话引导且奖励为物品，与授予 `[hell challenge]` 的解锁任务用途不同。撤回统一解锁时，应同步恢复区域条件和被移除的任务登记；只补回必要的缺失记录，保留现版独有的其他登记，不要用原版整份覆盖登记表。原版引用了未登记旧任务时，应单独说明，不能擅自补全成另一套流程。

前置关系以实际 `[pre required quest]` 引用为准。空块不能证明此前的对话任务是必做前置，也不能从章节名称、文件名或显示顺序推断任务链和最低等级。

任务中的接取/完成 NPC、副本、怪物、材料和奖励编号，要分别通过对应登记表解析。`[int data]` 按任务 `[type]` 和同类任务结构解释；修改后的任务目标可能与条件说明文字不一致，不能只根据文案判断完成方式。

`etc/hellparty.etc` 的 `[difficulty]` 属于深渊遭遇配置，和开启任务分开处理。PVF 恢复不会自动清空角色已保存的任务完成记录，须在游戏内核验。
