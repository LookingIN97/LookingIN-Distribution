# LookingIN Compute SDK v1

[简体中文](README.md) | **English**

LookingIN Compute is a local computation runtime maintained by LookingIN97.
The LookingIN plugin is only one consumer of the public SDK and is not required
to use the runtime. Third-party BepInEx plugins, desktop applications, and test
tools can use the same Client / Contracts interface.

## Distribution

After the first formal SDK release, this directory contains:

```text
LookingIN.Compute.Client.dll
LookingIN.Compute.Contracts.dll
README.md
README.en.md
LICENSE.txt
```

Client handles capability discovery, worker launch, IPC, cancellation, and
lifecycle. Contracts provides the stable input/output and service types. Use a
Client / Contracts pair from the same formal version.

These DLLs are **not player installation files**. Players continue to receive
the historical four-file LookingIN package through the existing updater. The
official plugin compiles the same public SDK sources directly into
`LookingIN.dll`.

## Supported integration boundary

Do not treat `LookingIN.dll` as an SDK, and do not depend on its internal types,
reflection names, or obfuscated implementation details. A third-party client
should own its state source and UI, then submit standardized input through the
Compute SDK:

```text
Third-party DLL / application
  -> LookingIN.Compute.Client
  -> LookingIN.Compute.Contracts
  -> LookingIN.Compute.Worker.exe
```

The Worker does not load third-party DLLs, read game state by itself, or perform
input actions.

## Minimal usage

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

A successful response is not automatically displayable. Clients should still
check `CanSimulate`, `CanDisplay`, `UnsupportedMechanisms`, censoring, and
early-termination metadata.

## Inputs and versions

Prefer public input contracts such as `RuntimeLiveSnapshotObservation` and let
the Worker perform rule projection. Do not conflate a missing attribute key
with an explicit zero, and do not substitute a stale snapshot when a new read
fails.

The compatibility promise for public API v1 and input schema v1 begins with
wire 69. Before connecting, the SDK uses `--describe` to check the API, schema,
wire range, and required capabilities. Player, Worker, and SDK/API versions are
separate version dimensions.

## License

The public SDK in this directory is provided under the [MIT License](LICENSE.txt).
The Worker implementation, Simulator, embedded Catalog, official plugin, and
other components not explicitly opened remain proprietary. The SDK license does
not extend to those components, LookingIN branding, or game data.

