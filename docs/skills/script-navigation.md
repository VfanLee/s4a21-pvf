# A21 脚本定位索引

这个索引只用于定位脚本和登记关系，不代替字段解释或修改流程。
表内是已记录的路径和标签，不是当次目标包的清单，也不证明客户端或服务端一定加载。
登记文件按 `.lst` 解析，路径统一分隔符后忽略大小写比较，不能把书签名称当成
字段说明或 ID。

修改过的包若与下列入口不同，以目标登记关系定位，并在修改前核对相应结构。

## 职业与技能

- 从 `character/character.lst`、`skill/skilllist.lst` 进入，再查职业技能表，
  不按职业名称拼文件名。
- 黑暗武士 `[demonic swordman]` 的角色文件为
  `character/swordman/dsswordman.chr`，技能登记表为 `skill/dsswordmanskill.lst`；
  技能定义仍放在 `skill/demonicswordman/`。
- SP、TP 界面分别从 `clientonly/skillshoptreespindex.co`、
  `clientonly/skillshoptreetpindex.co` 的 `[skill tree]` 追踪；引用相对于
  `clientonly/`。黑暗武士技能树仍使用 `skilltree/demonicswordman_sp.co`、
  `skilltree/demonicswordman_tp.co`。界面树不等于技能定义或等级数据。

## 配置入口

| 要查什么 | A21 路径 | 核对标签 |
| --- | --- | --- |
| 点券商城 | `etc/cerashop.etc` | `[item]`、`[avatar]`、`[premium]`，详见商城专题 |
| 售货机分组 | `etc/pcroom.vm`、`etc/pcroom4.vm` | `[item group]`、`[group num]`、`[material]`、`[output]` |
| 升级奖励活动 | `event/levelupreward.evt` | `[level up]`、`[grow type]`、`[send mail]`、`[inven]` |
| 签到活动 | `event/attendance.evt` | `[event]`、`[reward]`、`[final reward]`、`[send mail]` |
| 活动列表窗口 | `event/eventlistwindow.evt` | `[entry]`、`[event index]`、`[event info]`、`[link popupwindow type]` |
| 无限挑战奖励 | `etc/unlimitchallenge.etc` | `[onoff]`、`[server_id]`、`[level]`、`[reward_server]`、职业段 |
| 副本与任务排期 | `etc/chn_schedule.etc` | `[dungeon]`、`[quest]`、`[common schedule]`、`[dungeon minimap]` |
| 读条提示 | `etc/loadingadvermessage.etc` | `[loading message]`、`[dungeon loading message]`、`[town loading message]` |
| 道具重置、补充与邮件 | `etc/chn_server_limititemusageinfo.etc` | `[reset item]`、`[refill item]`、`[charac daily item mail]`、`[account refill item]` |
| 强化 | `etc/upgrade.etc` | `[table]`、`[table type]` |
| 增幅 | `etc/amplifyupgrade.etc` | `[table]`、`[cost]`、`[cost weights by rarity]`、`[stat weights by rarity]` |
| 锻造 | `etc/upgrade_separate.etc` | `[table]`、`[separate upgrade max]`、`[item weights by grade]`、`[separate upgrade effect]` |
| 红字属性配置 | `etc/amplifyitem.etc` | `[option mapping table]`、`[amplification rate by rarity]`、`[option data]`、`[purify material]` |
| 神秘商店 | `etc/secretshop.etc` | `[dungeon npc]`、`[level section]`、`[level npc]`、`[cash item]` |
| QP 商店 | `etc/questshop.etc` | `[quest status]`、`[quest status info]`、`[level info]`、`[quest point]` |
| 契约定义 | `etc/premiumlist_new.etc` | `[type]`、`[target item]`、`[attr]`、`[term]`、`[items]` |
| 契约展示 | `etc/premiumserviceeffect.etc` | `[premium service]`、`[type]`、契约图像索引 |
| SP 表 | `etc/sptable.etc` | `[sp table]` |
| 套装映射 | `etc/equipmentpartset.etc` | `[equipment part set]` |
| 效果套装 | `etc/equipmenteffectset.etc` | `[effect equipment part set]` |
| 账号仓库 | `etc/accountcargo.etc` | `[required level]`、`[upgrade info]` |
| 冒险团升级 | `etc/linksystem/charactermanage.etc` | `[point bonus]`、`[manage level point]` |
| 联动收益加成 | `etc/linksystem/characlinksystem.etc` | `[link level]`、低/高联动经验和金币段 |
| 支援兵资格 | `etc/characlinksystem.etc` | `[1st link character info]`、`[2nd link character info]` |
| 支援兵数量、时间与技能 | `etc/linksystem/striker.etc` | `[striker combo]`、`[common cool time]`、支援技能段 |

修改前继续核对记录、同用途近邻和读取方，不能单凭标签推断单位、权重、上限或开关。
活动配置还要查登记、启用条件及读取方；活动列表窗口不能直接当滚动公告，
不同奖励系统不能直接当作同一套升级邮件机制。

冒险团和支援兵字段解释见 [通用规则](README.md#支援兵配置参考-servers4a21)。
定位页不根据文件存在推断实际读取或功能生效。

## 如何核验书签

- 分别核对路径存在、正文可读且非空、登记引用和运行时读取。保留的空文件不证明数据生效。
- 其他版本路径、自定义道具范例、本地化 `.str` 路径只能用作查找线索，不能为了对上书签补建文件。
- NPC 商店必须闭合 `npc.lst → .npc [role] → itemshop.lst → .shp`，
  不能只看商店文件名或 `[NPC]` 回指。
- 掉落词典展示、掉落登记、实际生成表属于不同层；实际掉落来源按掉落专题核查。
