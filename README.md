# LookingIN Distribution

**简体中文** | [English](README.en.md)

LookingIN 的官方公共分发仓库。这里面向两类用户：

- **普通玩家**：从 GitHub Releases 获取和自动更新 LookingIN。
- **二次开发者**：从 `sdk/` 获取公开 Compute SDK，并通过受支持的接口调用计算核心。

> [!IMPORTANT]
> 这是**分发仓库，不是完整源码仓库**。LookingIN 插件和 Compute Worker 仍是
> 专有软件；公开 SDK 使用单独的 MIT 许可。公开二进制并不意味着整个项目开源。

## 普通玩家

请从 GitHub Releases 安装 LookingIN。玩家发布包继续保持历史四文件布局：

```text
LookingIN.dll
LookingIN.Compute.Worker.exe
LookingIN.Updater.exe
README.md
```

现有自动更新器会继续识别这个布局。更新流程会先验证签名更新清单和下载包的
SHA-256，再替换安装内容。

同一 Release 还提供五个可选独立插件；只需要单项功能时再安装：

- **SanityCheck**：背包价值与 PvE 遗漏提醒
- **LudicrousSpeed**：游戏速度控制
- **WitnessMe**：PvP 构筑、实战记录与 HTML 报告，不含预测
- **ThereYouAre**：PvP 对手名单提醒
- **PremiumTextures**：修复跨局后野怪物品预览可能变白且不恢复的问题

四者均独立自动更新。已安装完整 LookingIN 时无需再装；若同时存在，独立运行时休眠，
只保留自身更新检查。

`sdk/` 下的 DLL **不是玩家安装文件**。请不要把它们复制到
`BepInEx/plugins/LookingIN`，也不要手动双击 Worker 或 Updater。

## 合规与风险

The Bazaar 发布了官方 [Mod 政策](https://www.playthebazaar.com/mod-policy)。
LookingIN 主插件及 LudicrousSpeed 的部分功能不一定符合该政策的所有条款。
SanityCheck、WitnessMe 和 ThereYouAre 的功能范围相对较窄，但所有组件均为
未经官方审查或认可的第三方软件。请在安装前自行阅读上述政策并评估风险。

## 二次开发 / Compute SDK

LookingIN 将计算核心作为独立本机 Runtime 维护。第三方开发者不需要把
`LookingIN.dll` 当作开发依赖，也不需要修改官方插件 DLL；推荐方式是：

```text
你的 BepInEx DLL / 桌面程序
        │
        ├─ LookingIN.Compute.Client.dll
        └─ LookingIN.Compute.Contracts.dll
        │
        ▼
LookingIN.Compute.Worker.exe
```

公开 SDK 负责 Worker 能力发现、进程启动、IPC、请求取消、错误映射和生命周期；
Worker 负责规则投影、模拟和搜索。接口公开不代表 Worker 内部实现、Simulator 或
内嵌 Catalog 开源。

开发者入口：

- [Compute SDK 中文文档](sdk/README.md)
- [Compute SDK English documentation](sdk/README.en.md)
- [SDK MIT License](sdk/LICENSE.txt)

首次公开 SDK 正式发布前，`sdk/` 可能只有文档和许可；正式版本发布后，匹配该
版本的 `LookingIN.Compute.Client.dll` 和 `LookingIN.Compute.Contracts.dll` 会同步到
这里。SDK DLL 不作为玩家 GitHub Release 资产，也不进入自动更新 ZIP。

## 版本与兼容性

玩家版本、Worker 版本和公共 SDK/API 版本是不同的版本维度。第三方客户端应使用
同一版本配套的 Client / Contracts，并在连接前通过 SDK 的 capability discovery
检查 Worker 是否兼容；不要依赖 `LookingIN.dll` 的 internal 类型、反射名称或
Obfuscar 混淆后的实现细节。

Distribution 的版本 tag 会标记与对应 LookingIN 正式版本一起发布的 SDK 快照，
便于开发者定位历史兼容版本。

## 仓库内容

这个 Git 分支只保存需要长期公开维护的入口文件：

- `README.md` / `README.en.md`：仓库说明与语言入口；
- `sdk/`：开发者 SDK、文档和独立许可；
- [`LICENSE.txt`](LICENSE.txt)：LookingIN 专有 EULA；
- [`THIRD-PARTY-NOTICES.txt`](THIRD-PARTY-NOTICES.txt)：第三方软件声明。

玩家 ZIP、签名更新清单、校验和与正式 Release notes 作为 GitHub Release 资产维护，
不在 Git 分支中重复保存。

## 许可边界

LookingIN 主插件、Worker、Simulator、内嵌规则/Catalog 及未明确开放的其他组件均按
[`LICENSE.txt`](LICENSE.txt) 的专有条款提供。

`LookingIN.Compute.Client`、`LookingIN.Compute.Contracts` 及明确标记的 SDK 内容按
[`sdk/LICENSE.txt`](sdk/LICENSE.txt) 的 MIT License 提供。SDK 许可不会自动扩大到
Worker 内部实现、游戏数据、LookingIN 品牌或其他专有组件。

## 安全与更新

自动更新会验证签名清单以及下载归档的 SHA-256；发布流程还会校验归档文件白名单。
如果你只是普通玩家，请始终优先使用正式 Release 和内置更新路径，不要混用来源不明
的 Worker 或 SDK 二进制。

## 项目关系

LookingIN 是独立第三方项目，与 Tempo、The Bazaar、Unity、Microsoft 及其各自权利人
不存在隶属、赞助或官方支持关系。第三方名称和商标归其各自权利人所有。
