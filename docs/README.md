# 维护者文档

这份目录是**给你看的**学习笔记，不是给模型执行的操作手册。

改 PVF 时我们一起用同一套事实：模型读英文 `AGENTS.md` / `.agents/skills/`，你读这里的中文。两边说的必须一致，交流才不会跑偏。

## 怎么读

| 想了解 | 打开 |
| --- | --- |
| PVF 结构与 ID 关系 | [structure.md](structure.md) |
| 硬规则与通用修改流程 | [skills/README.md](skills/README.md) |
| 城镇/赛丽亚房间 NPC 放置与商店（`.shp`） | [skills/npc-shop.md](skills/npc-shop.md) |
| 点券商城的上架、售价、页面、契约、商品迁移 | [skills/cera-shop.md](skills/cera-shop.md) |
| 道具定义、NPC 道具价格、期限、礼包与任务奖励（`.stk` / `.equ`） | [skills/items.md](skills/items.md) |
| 金币掉落概率、数量、浮动、通关倍率 | [skills/gold-drop.md](skills/gold-drop.md) |
| 全局材料掉落、怪物物品掉落池、任务材料掉落概率与数量 | [skills/item-drop.md](skills/item-drop.md) |
| 副本难度表、原版对比与旧副本入口 | [skills/dungeon-difficulty.md](skills/dungeon-difficulty.md) |

## 和 skills 的对照

| 你读（中文） | 模型读（英文） |
| --- | --- |
| [structure.md](structure.md) | [`AGENTS.md`](../AGENTS.md) |
| [skills/README.md](skills/README.md) | [`.agents/skills/SKILL.md`](../.agents/skills/SKILL.md) |
| [skills/npc-shop.md](skills/npc-shop.md) | [`.agents/skills/npc-shop/SKILL.md`](../.agents/skills/npc-shop/SKILL.md) |
| [skills/cera-shop.md](skills/cera-shop.md) | [`.agents/skills/cera-shop/SKILL.md`](../.agents/skills/cera-shop/SKILL.md) |
| [skills/items.md](skills/items.md) | [`.agents/skills/items/SKILL.md`](../.agents/skills/items/SKILL.md) |
| [skills/gold-drop.md](skills/gold-drop.md) | [`.agents/skills/gold-drop/SKILL.md`](../.agents/skills/gold-drop/SKILL.md) |
| [skills/item-drop.md](skills/item-drop.md) | [`.agents/skills/item-drop/SKILL.md`](../.agents/skills/item-drop/SKILL.md) |
| [skills/dungeon-difficulty.md](skills/dungeon-difficulty.md) | [`.agents/skills/dungeon-difficulty/SKILL.md`](../.agents/skills/dungeon-difficulty/SKILL.md) |

## 同步约定

查询或修改 PVF 时，只要确认了新的、可复用的事实，交付前：

1. 先写进对应的 skill / `AGENTS.md`（模型下次按这个做）
2. **同一轮**写进这里对应的中文页（你下次按这个学、按这个对）
3. 内容精炼，直接写已确认的操作规则和字段含义，保留必要的版本/运行环境范围
4. 不写来源叙述、历史对比、参考包名称、一次性编号/数量或不确定的解释；后续使用不依赖旧任务附件
5. 新主题建立独立技能与中文页，并更新两边的入口；已有内容没有变化则不重复改写

只改一边，之后你我说的就不是同一套。docs 不替代 skill：模型改文件仍读 skill，不把整份 docs 灌进上下文。
