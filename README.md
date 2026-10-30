# LookingIN Distribution

<p align="center">
  <img src="assets/branding/lookingin-logo-512.png" alt="LookingIN" width="192">
</p>

**简体中文** | [English](README.en.md)

LookingIN 的官方公共分发仓库。这里面向两类用户：

- **普通玩家**：从 GitHub Releases 获取 LookingIN；自动更新首次优先 Cloudflare 镜像，并以 GitHub 兜底，成功后记住可用源。
- **二次开发者**：从 `sdk/` 获取公开 Compute SDK，并通过受支持的接口调用计算核心。

> [!IMPORTANT]
> 这是**分发仓库，不是完整源码仓库**。LookingIN 插件和 Compute Worker 仍是
> 专有软件；公开 SDK 使用单独的 MIT 许可。公开二进制并不意味着整个项目开源。

## 普通玩家

先从 [Bazaar++（BPP）下载页](https://bazaarplusplus.com/download) 安装 BPP。BPP 会一并安装 BepInEx，无需手动安装 BepInEx；确认游戏能够启动后退出游戏，再安装 LookingIN。

请从同一个 GitHub Release 选择对应平台的 LookingIN ZIP，解压后将整个 `LookingIN` 文件夹放入 `The Bazaar/BepInEx/plugins/`。Windows 使用 `win-x64` 包，继续保持历史四文件布局：

```text
LookingIN.dll
LookingIN.Compute.Worker.exe
LookingIN.Updater.exe
README.md
```

现有自动更新器会继续识别这个布局。更新流程会先验证签名更新清单和下载包的
SHA-256，再替换安装内容。

Apple Silicon Mac（macOS 14+）使用 `macos-arm64` 包；首次或手动升级后运行文件夹中的 `AllowRun.command`。Mac 包含原生计算进程、更新器和加密库，不能只复制 DLL。目前没有 Apple 公证，Intel Mac / Rosetta 不在支持范围内。

Mac 三段正式版本使用独立的平台更新清单，验证后下载，游戏退出后替换，下次启动生效。旧四段测试版需先手动安装一次正式包。Windows 与 Mac 共用规则和内置资源。

从 0.248.0 起仅提供 LookingIN 主包，不再分发或更新单项功能插件。背包提示、加速、
实战报告、对手名单提醒和一键投降继续在主插件中维护。PremiumTextures 专属修复随其退役。
旧单项插件不会自动
卸载或迁移数据；改用主插件时，请退出游戏后移除旧插件目录，另行保留所需配置与报告。

`sdk/` 下的 DLL **不是玩家安装文件**。请不要把它们复制到
`BepInEx/plugins/LookingIN`，也不要手动双击 Worker 或 Updater。

## 合规与风险

The Bazaar 发布了官方 [Mod 政策](https://www.playthebazaar.com/mod-policy)。
LookingIN 的部分功能不一定符合该政策的所有条款。本项目为未经官方审查或认可的
第三方软件。请在安装前自行阅读上述政策并评估风险。

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
