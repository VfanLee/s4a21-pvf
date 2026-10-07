# NPC 商店

## 修改边界与回读

- 登记链：NPC → `.npc` 商店角色 → 商店登记 → `.shp`；商品再查道具/装备登记。
- 上架只改指定页签、分类和物品 ID；放置只改指定地图演员或坐标，核对共用地图。
- 保留未授权的角色绑定、商店类型、其他列表与道具效果；价格、期限在道具文件。
- 回读实际路径、商品顺序和引用；外观、页签与实际购买分别验收。

## NPC 位置与商店登记

`itemshop/itemshop.lst` 只登记商店，不负责让 NPC 出现在地图中。
A21 地图 `.map` 的 `[NPC]` 每个条目包含五项：NPC ID、反引号方向
（`[left]` / `[right]`）、X、Y、整数标志值，段落以 `[/NPC]` 闭合。
NPC ID 经 `npc/npc.lst` 核对，末尾标志按目标地图已有条目保留，不猜测含义。

放置已有 NPC 时，在实际加载的房间或城镇地图中追加位置即可，不必新建 NPC 或商店 ID。
新增 NPC 则需要空闲 ID、NPC 登记、`.npc` 定义和地图位置；要开商店还需闭合
NPC 的商店角色、商店登记和 `.shp`。
城镇从已登记 `.twn` 的区域地图引用查找，引用可能相对 `map/`，不一定相对档案根目录。
赛丽亚房间可能保留 `map/common/`、`map/town/common/` 两套地图及普通、PVP、活动版本，
必须确认目标客户端实际加载哪一份，不能只靠文件名选文件；未出现在 `map.lst` 也不证明城镇地图无效。
保留已有 NPC，实机核对位置、可行走范围、外观、交互及商店功能。
仅水平移动时，减小 X 向左、增大 X 向右；保留 Y、朝向与末尾标志。

对应 [`.agents/skills/npc-shop/SKILL.md`](../../.agents/skills/npc-shop/SKILL.md)。价格、效果见 [items.md](items.md)。
点券商城的商品、售价与页面使用独立的 [cera-shop.md](cera-shop.md)。

**页签** = `[tab]`。**分类** = `[use category]` / `[category entry]`。两者不是同一层。

改列表：动 `.shp`。改买价 / 回收 / 兑换：动道具文件。

下面示例是**成对闭合的结构骨架**。实机文件还有未列出字段，改时保留。

按 NPC ID 搜索位置可能同时命中普通、镜像、梦境及活动地图；与已登记 `.twn`
区域引用核对。“几个身位”不是固定单位；保持原 Y 调整 X 后，实机检查间距。

## 文件说明

| 文件 | 作用 |
| --- | --- |
| `npc/npc.lst` | NPC ID → `.npc` 路径 |
| `npc/*.npc` | NPC 定义；`[role]` 里 `[item shop]` 后面是**商店 ID** |
| `itemshop/itemshop.lst` | 商店 ID → `.shp` 路径 |
| `itemshop/*.shp` | 商店上架列表 |

三种编号不要混，正向闭合见 [../structure.md](../structure.md)。

实际 NPC、商店和物品 ID 均从当次目标读取，不按文件名或示例编号套用。

其他入口还可能是 `[product item]`、`[secret shop]`。秘密商店写在文件里，不等于游戏里一定能打开。

新建商店：同时写 `itemshop.lst` 和 NPC 的 `[item shop]`。改已有商店：保留这些编号关系。

修改登记 ID 时，需要同时更新引用：NPC ID 对应地图 `[NPC]`、商店 `[NPC]` 回指和
适用的对话说话人标记，迁移已有 NPC 还应检查任务等引用；商店 ID 对应 NPC `[role]`
中的商店绑定。只改登记表会留下旧引用，导致地图 NPC 无法解析或商店无法打开。
仅修改名称、外观时，优先保留原 ID。
新编号从当前目标的对应登记表中选取，并检查重复 ID、重复路径登记。未占用区间只是
这份目标的数据检查结果，不能作为通用安全区间或客户端支持的证明；NPC、商店编号各自独立。
不要在地图里全局替换同一个数字：`[day attacked monster info]` 等城镇侵袭配置
可能引用数值相同的地下城 ID，应单独查 `dungeon/dungeon.lst`，修改 NPC 放置时保留。

## 字段说明

| 标签 | 含义 | 注意 |
| --- | --- | --- |
| `[NPC]` | 回指 NPC ID | 不能替代 `npc.lst` → `[item shop]` 那条正向链 |
| `[type]` | 商店类型 | 沿用原值，如 `[etc shop]`、`[expert shop]`；不要换成未核对类型 |
| `[sell info]` … `[/sell info]` | 售卖区 | 页签或商品都写在这里 |
| `[tab]` … `[/tab]` | 一个页签 | 反引号文本是页签名；每个页签自己带 `[item list]` |
| `[item list]` … `[/item list]` | 物品 ID 列表 | 只写 ID；空格 / Tab / 换行等效；禁止逗号；换行不会在游戏里分组 |
| `[use category]` | 商店级分类开关 | 如 `basic job`；每个页签仍可自选分类列表或普通列表 |
| `[category entry]` … `[/category entry]` | 一个职业/分类块 | 内含 `[id]`（分类编号，不是商店 ID 或物品 ID）和 `[item list]` |
| `[message]` | 商店对话 | 反引号字符串 |
| `[use toggle]` / `[expert job level]` | 副职业商店限制 | 不要抄到普通商店 |
| `[one a day start time]` / `[one a day item]` | 每日轮换 | 不是把 `[item list]` 换个位置 |

负数（`-1` `-2`）不是商品 ID。商品 ID 要按上下文走 `stackable.lst` 或 `equipment.lst`，不能从位数猜类型。

## 结构速查

### 外层骨架

自定义 NPC 不显示时，分开检查地图放置与登记、显示资源、商店解析。
含 `[field animation]`、`[role]`、`[dialog]` 等 NPC 字段，却没有商店售卖定义的
`.shp` 不是正确商店模板；仅改扩展名不会把 NPC 定义转换成商店。
把 `.npc` 移到子目录时，需核对动画依赖及客户端解析路径的基准目录；
诊断可先沿用已正常工作的目录布局。不能仅因商店数据错误就认定它导致 NPC 不显示。
人物不可见但能打开带名字的交互菜单，说明 NPC 已生成；应优先检查
`[field animation]` → `.ani` → `[IMAGE]` 的显示链，不必先换 ID 或坐标。
菜单中有商店选项，不证明商店能打开或购买成功。对比动画时同时检查解析后的内容和
引用路径；两份 `.ani` 相同，也不证明移目录后能找到动画或客户端 NPK 包含所需图像。
复用外观时，将 `[small face]`、`[big face]`、`[popup face]`、`[field animation]`
写在自定义 `.npc`；环境与对话语音字段也属于 `.npc`。自定义名称和商店绑定独立保留，
仅复用外观不需要复制整套对话与好感度记录。

### 对话说话人名称与立绘（A21）

台词中的 `<npc::ID>` 指定说话人，非零编号通过 `npc/npc.lst` 查对应 NPC；
它与承载台词文件的 `[name]`、`[field name]` 独立。复制 NPC 后只改名称，
不会自动替换台词中的说话人编号。检查普通 `[dialog]`、好感度和赠礼台词；
自定义 NPC 自己说的话使用其登记 ID，保留玩家台词 `<npc::0>` 及有意引用的其他 NPC。
外观字段独立保留。回读说话人标记后，加载更新的 PVF，实机核对对话名称与立绘。

对话立绘另有登记表：`etc/dialogwindowimageindex.etc`。

| 段落 | 内容 |
| --- | --- |
| `[npc image index pair]` | IMG 路径，随后为成对的 NPC ID、图片索引 |
| `[npc illust image index pair]` | 独立的好感度立绘映射 |
| `[foreign npc image index pair]` | 使用另一份本地化 IMG 的映射，需独立核对 |

`<npc::ID>` 的说话人需要对应立绘映射；只复制 `.npc` 的头像字段不会自动登记。
自定义 NPC 复用原有立绘时，在同一 IMG 对应段落中，用自定义 NPC 的实际登记 ID
配上原说话人的图片索引，保留其他条目。修改 NPC ID 时也要同步该映射。
索引仅对其 IMG 有效，不能跨资源路径照抄数字；没有客户端证据时不推断本地化段的优先级。
不要整表覆盖其他 PVF 的内容。回读检查成对结构、目标映射唯一及无关条目未变，
再实机验证普通对话和适用的好感度对话。
新增编号对放在对应段落的闭合标签之前，保留 IMG 路径；数字项数量必须为偶数。
`.npc` 头像索引与对话立绘索引指向不同资源，两者不必相同。

若对话沿用上一位 NPC 的立绘，优先检查此表是否缺少说话人映射；残留图片不代表
配置成了那个 NPC，也不能证明 ID 超出限制。映射存在时，再检查资源可用性与客户端读取、赋值。

`[favor sharing]` 与立绘、对话字段分开核查。记录的 ServerS4A21 `NpcFile` 解析器
不读取此标签，好感度存储按请求中的 NPC ID 区分；PVF 中存在此字段不证明服务端
已实现好感度共享。客户端处理和其他服务端版本需另行验证。

页签内的最小示意（占位符不可直接导入）：

```text
[tab]
    `<页签名>`
    [item list]
        <物品ID> <物品ID>
    [/item list]
[/tab]
```

| 结构 | 核对规则 |
| --- | --- |
| 外层 | `[NPC]` 回指、原 `[type]`、`[sell info]` 及其闭合；不要复制 NPC 定义字段 |
| 多页签 | 各 `[tab]` 同级，每个页签独立 `[item list]`；列表顺序为展示顺序 |
| 职业分类 | `[use category]` 为商店级；分类页签使用 `[category entry]`，包含 `[id]` 及其列表 |
| 分类与普通列表混用 | 同一商店不同页签可以采用不同列表结构，不嵌套第二个列表 |
| `basic job` | 编号 `0,1,5,3,4,2,6,7,8,11,10,9,12,13`；不能套到 `job`、`expert job`、`pvp job` |

### 无页签 / 每日轮换

- `StackableShop1.shp`：`[sell info]` 下直接 `[item list]`。
- `AbelroExpert.shp`（`[expert shop]`）：`[sell info]` 下直接 `[category entry]`，并带 `[use toggle]` / `[expert job level]`。
- `OneADayItemShop.shp`：普通 `[item list]` 可为空，轮换写在 `[one a day start time]` / `[one a day item]`。

参考 `PvfLib.ItemShopFile` 不提取无页签商品，也不递归 `[category entry]`，属于工具索引限制。
不能据此认定实际商店无效；沿用目标结构，界面和购买分别检查。客户端/服务端交易冲突先调查。

## 改哪里

| 目的 | 改哪个文件 |
| --- | --- |
| 上架 / 下架 / 换页签 | `.shp` 的 `[item list]` / `[tab]` |
| 对话说话人名称 / 立绘 | `.npc` 台词标记 / `etc/dialogwindowimageindex.etc` 对应说话人的编号对 |
| 金币买价 | 道具 `[price]`（普通可堆叠没有则回退 `[value]`） |
| 材料兑换 | 道具 `[need material]`；材料 ID 再走 `stackable.lst` |
| 点券商城 | 转到独立的 [cera-shop.md](cera-shop.md) |
| 胜点 / 其他货币 | 先追踪目标客户端和服务端的处理路径；不能只凭 `[medal]` 推断定价方式 |

实机：找到 NPC → 打开商店 → 核对页签 / 职业分类 / 顺序 → 看价格 → 试买或兑换 → 看中文是否乱码。
