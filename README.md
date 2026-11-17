# LookingIN

《The Bazaar》第三方战斗辅助工具，提供战斗预测、构筑分析与复盘。

**[官方网站](https://looking1n.com/) · [下载与安装](https://looking1n.com/download/) · [帮助与反馈](https://looking1n.com/help/)**

## 看清战斗，再做选择

LookingIN 把战前判断、构筑调整和赛后复盘放在一起。完整的实机图文说明见[官网功能介绍](https://looking1n.com/tutorial/#features)。

| 你想做什么 | LookingIN 提供什么 |
| --- | --- |
| 选野怪前权衡奖励与风险 | **PvE 胜率**：查看当前构筑对不同野怪的模拟胜算。 |
| 为下一场 PvP 找到参照 | **幽灵预测**：与同赛季、同天数、同角色的历史对手比较；不读取下一位真实对手。 |
| 判断调整是否真的变强 | **伤害／防御曲线**：查看不同时点的表现，用基准线比较调整前后。 |
| 买下、升级或附魔前先试算 | **构筑预览**：比较支持的选项对曲线的影响，不代替你购买或出售。 |
| 固定核心物品，尝试其他摆法 | **排列优化与 PIN**：固定指定位置，搜索排列、应用推荐并撤销；支持背包候选替换试算。**此项仅 Windows／macOS。** |
| 理解一场战斗为什么会赢或输 | **赛后胜率与触发详情**：正式 PvP 首次结算后查看实际对手的模拟胜率，并分析有记录的物品启动顺序与联动。 |
| 一局结束后逐天回顾 | **离线复盘**：查看 HTML 报告中的构筑、预测与战果，导出图片或复制构筑分享串。 |
| 收藏阵容、尝试其他人的思路 | **构筑库与斗蛐蛐**：按角色和天数筛选当前赛季构筑，导入、收藏并进行自定义对战。 |

不同平台的可用功能与操作方式以当前正式版设置和[安装教程](https://looking1n.com/tutorial/#installation)为准。模拟胜率不是胜负保证；历史幽灵样本、真实对手模拟和实际战果不能混为一谈。

## 下载与平台

| 平台 | 使用方式 |
| --- | --- |
| Windows x64 | 按官网教程先安装 BPP，再安装 LookingIN 完整文件夹。 |
| Apple Silicon Mac | macOS 14+ 与 BPP Mac；不支持 Intel／Rosetta，当前未做 Apple 公证。 |
| Android | 使用 **BPPLKI APK** 加载手机上已安装的官方游戏；只有游戏版本通过本包适配检查才启动，不使用桌面 ZIP。 |

版本、安装包和校验值统一在[官网下载页](https://looking1n.com/download/)提供。更新时覆盖原安装，不要先卸载或清除数据。LookingIN 并非游戏官方产品；请先阅读官网的[使用许可与风险说明](https://looking1n.com/policies/#license)。

## 分发与 SDK 公告

自 2026 年 11 月 17 日起，本 GitHub 仓库仅保留项目介绍与官网入口，不再提供安装包、Releases、SDK、历史文件或 Issues。软件下载与版本信息请以官网为准，安装包和自动更新由 Cloudflare 提供。旧版无法自动更新时，请从官网下载并覆盖安装。

LookingIN Compute SDK 已结束公开发布与支持，不再提供新版 SDK、第三方接入支持或未来兼容性保证。历史已发布 SDK 的 MIT 许可保持不变；LookingIN 主插件、Worker 等专有组件的许可不因此改变。使用许可与第三方声明可从官网的[隐私与许可](https://looking1n.com/policies/#license)页面查看。

## English

LookingIN is an independent third-party companion for *The Bazaar*, offering battle predictions, build analysis, and match review.

**[Official website](https://looking1n.com/en/support/) · [Downloads](https://looking1n.com/en/download/) · [Help and feedback](https://looking1n.com/en/help/)**

### Understand the fight before making a choice

See the [illustrated feature guide](https://looking1n.com/en/tutorial/#features) for real examples.

| Your goal | What LookingIN offers |
| --- | --- |
| Weigh a monster's reward against the risk | **PvE estimates** for your current build against available monsters. |
| Put an upcoming PvP fight in context | **Ghost predictions** using historical opponents from the same season, day and hero—not your next real opponent. |
| Compare a build before and after a change | **Damage and defense curves**, with a baseline for comparison over time. |
| Evaluate a purchase, upgrade or enchantment | **Previews** for supported options without buying or selling on your behalf. |
| Keep key items fixed while trying arrangements | **Optimization and PIN**, with apply/undo and stash replacement trials. **Windows/macOS only.** |
| Understand an individual fight | **Post-battle PvP estimates and item trigger details**, where recorded, for examining outcomes and interactions. |
| Review a completed run day by day | **Offline HTML reports**, image export and build sharing. |
| Save builds and test other players' ideas | **Build library and custom battles** with hero/day filters, imports and favorites within the current season. |

Features and controls vary by platform; consult the current release and [installation guide](https://looking1n.com/en/tutorial/#installation). Estimates do not guarantee a result. Historical samples, simulations against actual opponents and observed outcomes are different kinds of information.

### Platforms

Windows x64 requires BPP. Apple Silicon Mac requires macOS 14+ and BPP Mac; Intel/Rosetta are not supported and the package is not Apple-notarized. Android uses the separate BPPLKI APK to load an installed official game only when its build is supported. All downloads and checksums are on the [official website](https://looking1n.com/en/download/); update in place without uninstalling or clearing data. LookingIN is not endorsed by the game developer.

### Distribution and SDK notice

Since November 17, 2026, this repository serves as a project overview and website entry point, not a software distribution channel. GitHub downloads, Releases, public SDK distribution, and Issues have been discontinued. Use the official website for downloads and version information; packages and updates are served by Cloudflare. Install the website download over your existing installation if an older version can no longer update automatically.

Public LookingIN Compute SDK releases and support have ended. Previously released SDK versions retain their MIT license; this does not change the licenses of proprietary components or promise compatibility with future versions. See the website's [license information](https://looking1n.com/en/policies/#license).
