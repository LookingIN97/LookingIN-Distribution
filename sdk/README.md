# LookingIN Compute SDK v1

**简体中文** | [English](README.en.md)

LookingIN Compute 是由 LookingIN97 维护的本机计算核心。LookingIN 插件是公共
SDK 的一个使用者，不是运行核心的前置条件。第三方 DLL、桌面应用和测试程序
使用相同的 Client/Contracts 接口。核心不加载第三方 DLL，不读取游戏状态，
也不执行鼠标、键盘或游戏操作。

## 组件与依赖

| 组件 | 职责 | 目标框架 |
| --- | --- | --- |
| `LookingIN.Compute.Contracts` | 输入、输出、服务接口、协议与输入校验 | `netstandard2.0` |
| `LookingIN.Compute.Client` | 能力发现、进程启动、IPC、取消、进度与清理 | `netstandard2.1` |
| `LookingIN.Compute.Worker` | 观察投影、模拟器、搜索和内嵌 Catalog | `net10.0`，生产分发为 NativeAOT |

第三方客户端只引用 Client，传递引用取得 Contracts。不要引用官方 `LookingIN.dll`
或 Simulator，也不要自行复制 Contracts 源码；一个客户端进程内应使用同一份公共
契约类型。官方 LookingIN 为保持历史四文件更新布局，会在构建时静态编入这两套
公共源码，这是参考插件的分发选择，不是第三方集成要求。

当前 SDK 不通过公共 NuGet feed 发布。正式版本发布时，匹配该版本的
`LookingIN.Compute.Client.dll` 与 `LookingIN.Compute.Contracts.dll` 会同步到公开
`LookingIN-Distribution` 仓库主分支的 `sdk/` 目录；Distribution 的版本 tag 会保留
对应历史。两个 DLL 不属于玩家 GitHub Release 资产，也不进入自动更新 ZIP。
核心可以保持专有，SDK 和示例的许可见 `LICENSE.txt` 以及仓库根许可证中的范围说明。

两个 SDK 项目均声明 `PackageReadmeFile`，显式打包时会把本说明与 MIT 文本
带入各自的 NuGet 包。普通 build 不打包、不上传；维护者分发 SDK 时需要同时
提供 Client 与匹配的 Contracts 包，不能只复制主 DLL 或依赖私有仓库文档链接。

## 最小调用

以下代码要求宿主支持 .NET Standard 2.1。`workerPath` 必须为可信的 Worker
可执行文件绝对路径，不是 DLL 路径，也不是来自网络输入的任意可执行文件。
开发构建可以使用 Worker 的 apphost；Windows 生产使用 NativeAOT EXE。

```csharp
using LookingIN.Compute.Client;
using LookingIN.Compute.Contracts;

using var client = new ComputeWorkerService(new ComputeClientOptions
{
    WorkerPath = workerPath,
    WorkerCapacity = 2,
    ClientVersion = "MyClient/1.0",
    RequiredCapabilities = new[] { ComputePublicApi.AverageTimeline },
    Log = message => logger(message)
});

var result = await client.SimulateAverageTimelineAsync(
    observation,
    maximumMilliseconds: 10000,
    sampleIntervalMilliseconds: 100,
    runCount: 8,
    cardImpactMaximumMilliseconds: 10000,
    masterSeed: 1,
    cancellationToken: cancellationToken);

// 成功收到响应并不等于结果可显示。检查 CanSimulate、CanDisplay、
// UnsupportedMechanisms、截断/提前终止信息，再决定如何展示。
```

源码维护树中另有 `samples/LookingIN.Compute.Sample` 用固定观察做完整回归；
Distribution 只发布 SDK DLL、本文和 MIT 许可，不要求开发者取得私有源码。
实际客户端应自行构造或采集 `RuntimeLiveSnapshotObservation`，并把已安装或
另行取得的可信 Worker 绝对路径传给 Client。不要将历史样例的 Catalog 身份
伪装成当前游戏输入的身份。

## 运行与生命周期

一个 `ComputeWorkerService` 实例拥有一个子进程及一条连接，可并发提交请求。
用完必须 Dispose；Windows 客户端在 Worker 执行前将其放入私有 Job，宿主
异常退出也会清理。Worker 还监视宿主退出；Linux 便携启动路径主要用于
离线开发和回归，不应把 Windows 原生身份核对误认为跨平台等价保证。

SDK 在构造时先执行 `--describe`，检查公共 API、输入 schema、wire 范围、
容量和必需能力，再通过继承的匿名管道传递会话秘密。实际计算走本机
Named Pipe 与认证的二进制帧。秘密不放在命令行或环境变量中；帧大小和
资源边界不会因为公开 API 而取消。

`StartupTimeoutMilliseconds` 是各启动阶段的上限，不是整个计算请求的
执行时限。计算超时应由调用者的 CancellationToken 实现。日志目录可配置，
多实例应分配独立目录以便诊断。`--describe` 会执行你指定的文件，并不是
可执行文件签名校验；宿主/安装器仍应验证来源和完整性。

当前不是公共多客户端守护进程。不要让多个客户端抢连同一个会话；多个
插件各自创建实例会各自占用 CPU 和内存，宿主应协调并行度。排列结果句柄
只在所属会话有效，Worker 只保留最近 8 份；不能跨实例、跨重启或长期存盘。

## 输入契约

优先提交 `RuntimeLiveSnapshotObservation`，由核心完成规则投影，避免在
客户端重复实现光环还原、基础属性恢复和计算规则。输入来源必须明确：
实时规划、已物化状态、PVP 开局证据、离线构筑不是可互换的数据。

属性字典必须区分“键不存在”与“键存在且为 0”；实例 ID 不是模板 ID；
区域、占位、品质、附魔和标签必须使用其实际含义。PVP 应使用独立的
`RuntimePvpEvidence`，保留冻结基线与稀疏开战补丁的区别。

分享码捕获只支持接口规定的真实规划来源，不能混入假想预览，并要求输入
Catalog 与核心一致。`ExpectedCatalogIdentity` 可在握手后拒绝不匹配的
规则输入；省略它不表示 SDK 已经证明输入来自正确的游戏版本。游戏适配器
负责实际游戏版本检查，离线客户端负责选择和声明其数据来源。

LookingIN 会同时声明自己审核过的 GameData 身份，不能把 Worker 返回的身份
再作为“预期身份”来完成自证。该声明由 `tools/generate-plugin-input-catalog.py`
从 provenance 生成；它绑定 GameData 来源，而不是编译后的 Worker 哈希。
同一份 GameData 下的计算修复不要求更改此身份。更换游戏数据时需要更新并
验证适配器声明；发布前 `--check` 会拒绝过期的生成文件。

结构校验不代替领域语义校验。核心可能返回不支持机制、不可模拟或截断结果，
客户端不得把这些结果默认为可靠预测。不要把已经应用过的加成再当成中性
基础值，也不要用旧快照冒充本次失败的读取。

## 版本与更新

首版公开 API 主版本为 1，输入 schema 为 1，首个公开 wire 修订为 69；
原先私有 wire 68 及更早版本不属于兼容承诺。Worker 版本独立于插件版本。

同一公共主版本内保留既有方法、字段及其编号。新增可选字段或能力必须
保持已有语义，不能仅扩大版本接受范围就认定兼容。当前只发布了一个公共
wire 修订；将来新增修订前必须运行旧客户端二进制对新核心的回归。

维护源码中的 `sdk/public-api-v1.txt` 检查公共类型和成员签名，协议结构基线
检查编码变化。
二者都不能独自证明行为兼容。默认值、错误、输入解释以及规则变更仍需
语义回归；涉及旧插件没有采集的新信息时，可能必须升级游戏适配器。

核心不自动联网自更新。维护者独立发布可信 Runtime，由客户端或用户选择
兼容版本。遇到不兼容主版本应拒绝替换或并存安装，不能无条件覆盖成最新
EXE。正在运行的会话不支持热替换或继续使用重启前的结果句柄。

## LookingIN 玩家更新兼容

LookingIN 的玩家包始终保留原有四文件白名单：插件 DLL、Worker、Updater 和
README。公共 Client / Contracts 源码静态编入插件，因此旧更新器无需认识 SDK
文件，也不需要中间桥接版本。自动更新继续沿用原有签名清单、ZIP 哈希、文件白名单
和事务替换流程；开发者 SDK 只通过 Distribution Git 分支发布，不参与玩家更新。

## 维护者回归入口

```bash
dotnet run --project tests/LookingIN.Compute.Client.Verification -c Release
dotnet run --project tests/LookingIN.Compute.Verification -c Release
python3 tools/verify-compute-protocol.py --check
python3 tests/LookingIN.Compute.Protocol.Verification/test_verify_compute_protocol.py
```

公共 API 基线只能由维护者在明确审查后更新；不能为掩盖破坏性改动直接刷新。
NativeAOT 与 Windows 实际启动仍需单独验证，托管构建通过不能替代它们。
