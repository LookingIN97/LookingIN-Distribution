# LookingIN Compute SDK v1

**简体中文** | [English](README.en.md)

LookingIN Compute 是由 LookingIN97 维护的本机计算核心。LookingIN 插件只是公共
SDK 的一个使用者，不是调用核心的前置条件。第三方 BepInEx DLL、桌面程序或测试
工具可以使用同一套 Client / Contracts 接口。

## 获取方式

正式 SDK 发布后，本目录会包含：

```text
LookingIN.Compute.Client.dll
LookingIN.Compute.Contracts.dll
README.md
README.en.md
LICENSE.txt
```

Client 负责能力发现、Worker 启动、IPC、取消和生命周期；Contracts 提供稳定的输入、
输出和服务接口。两个 DLL 应按同一正式版本配套使用。

这些 DLL **不属于玩家安装包**。LookingIN 玩家仍只通过四文件 Release 和原有自动
更新器升级；官方插件会把相同的公共 SDK 源码静态编入 `LookingIN.dll`。

## 推荐的集成边界

不要把 `LookingIN.dll` 当作 SDK，也不要依赖其 internal 类型、反射名称或混淆后的
实现细节。第三方程序应自行负责状态来源和 UI，只把标准化输入交给 Compute SDK：

```text
第三方 DLL / 应用
  -> LookingIN.Compute.Client
  -> LookingIN.Compute.Contracts
  -> LookingIN.Compute.Worker.exe
```

Worker 不会加载第三方 DLL，也不会自行读取游戏状态或执行输入操作。

## 最小调用

```csharp
using LookingIN.Compute.Client;
using LookingIN.Compute.Contracts;

using var client = new ComputeWorkerService(new ComputeClientOptions
{
    WorkerPath = workerPath,
    WorkerCapacity = 2,
    ClientVersion = "MyClient/1.0",
    RequiredCapabilities = new[] { ComputePublicApi.AverageTimeline }
});

var result = await client.SimulateAverageTimelineAsync(
    observation,
    maximumMilliseconds: 10000,
    sampleIntervalMilliseconds: 100,
    runCount: 8,
    cardImpactMaximumMilliseconds: 10000,
    masterSeed: 1,
    cancellationToken: cancellationToken);
```

成功返回不代表结果一定适合展示。客户端仍应检查 `CanSimulate`、`CanDisplay`、
`UnsupportedMechanisms`、截断和提前终止信息。

## 输入和版本

优先使用 `RuntimeLiveSnapshotObservation` 等公开输入契约，让 Worker 负责规则投影。
不要把“键不存在”和“值为 0”混为一谈，也不要用旧快照代替一次失败的新读取。

公开 API v1、输入 schema v1 从 wire 69 开始形成兼容承诺。连接时 SDK 会先通过
`--describe` 检查 API、schema、wire 范围和 capability。玩家版本、Worker 版本和
SDK/API 版本并不是同一个版本号。

## 许可

本目录中的公开 SDK 按 [MIT License](LICENSE.txt) 提供。Worker 实现、Simulator、
内嵌 Catalog、官方插件和未明确开放的其他组件仍为专有软件。SDK 许可不扩大到这些
组件，也不授予 LookingIN 品牌或游戏数据的额外权利。

