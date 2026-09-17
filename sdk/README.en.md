# LookingIN Compute SDK v1

[简体中文](README.md) | **English**

LookingIN Compute is a local computation runtime maintained by LookingIN97.
The LookingIN plugin is one consumer of the public SDK; it is not a prerequisite
for using the runtime. Third-party BepInEx plugins, desktop applications, and
test tools can use the same Client/Contracts API. The runtime does not load
third-party DLLs, read game state on its own, or perform mouse, keyboard, or
gameplay actions.

## Components

| Component | Responsibility | Target framework |
| --- | --- | --- |
| `LookingIN.Compute.Contracts` | Input/output models, service interfaces, protocol contracts, input validation | `netstandard2.0` |
| `LookingIN.Compute.Client` | Capability discovery, worker launch, IPC, cancellation, progress, cleanup | `netstandard2.1` |
| `LookingIN.Compute.Worker` | Observation projection, simulation, search, embedded catalog | `net10.0`; distributed as NativeAOT on Windows |

Client applications should reference `LookingIN.Compute.Client`; the Contracts
assembly is brought in as a dependency. Do not reference the official
`LookingIN.dll` or the Simulator, and do not copy the Contracts sources into
your own assembly. A client process should use one matching public contract
type identity.

The public Distribution repository contains the matching
`LookingIN.Compute.Client.dll` and `LookingIN.Compute.Contracts.dll` under
`sdk/`. Those developer DLLs are not part of the player update ZIP. LookingIN
itself compiles the same public Client/Contracts sources into its own obfuscated
plugin assembly so existing four-file automatic updates remain compatible.

The SDK and samples are MIT-licensed. The worker implementation, simulator,
embedded catalog, and official plugin remain proprietary; see `LICENSE.txt`
and the repository-level license notice for the exact boundary.

## Minimal usage

The host must support .NET Standard 2.1. `WorkerPath` must point to a trusted
worker executable by absolute path. It is an executable path, not a DLL path,
and should not be populated from untrusted network input.

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

// A successful response does not automatically mean it is displayable.
// Check CanSimulate, CanDisplay, UnsupportedMechanisms, censoring, and
// early-termination metadata before presenting the result.
```

The complete fixed-input example lives in `samples/LookingIN.Compute.Sample`.
It does not launch the game or reference the official plugin. Sample card and
attribute values are rule examples maintained with the runtime; they are not a
recipe for reading a live game state.

## Process and lifecycle

One `ComputeWorkerService` owns one worker process and one session. Requests may
run concurrently. Dispose the client when finished. On Windows, the client puts
the worker in a private Job before managed execution starts, so abrupt host
termination also cleans up the worker. The worker independently monitors its
parent process.

The SDK runs `--describe` before opening a compute session. It validates the
public API major version, input schema, supported wire range, capacity, and
required capabilities. Session material is delivered through an inherited
anonymous pipe, while computation uses a local named pipe and authenticated
binary frames. Session secrets are not placed on the command line or in the
environment.

`StartupTimeoutMilliseconds` limits startup stages; it is not a timeout for
long-running computation. Use `CancellationToken` for request cancellation.
Multiple SDK clients create multiple worker processes and therefore consume
independent CPU and memory budgets. Placement handles are session-local and the
worker only keeps the most recent eight results.

## Input contract

Prefer `RuntimeLiveSnapshotObservation` and let the worker perform rule
projection. Do not duplicate aura restoration, base-stat recovery, or combat
rule materialization in the client. Live planning state, materialized combat
state, PVP opening evidence, and offline builds are different input boundaries
and must not be treated as interchangeable.

Attribute dictionaries distinguish a missing key from an explicit zero.
Instance IDs are not template IDs. Zone, footprint, tier, enchantment, and tag
fields must represent the actual observation. PVP callers should use
`RuntimePvpEvidence` so the frozen baseline and sparse combat-start patch retain
their separate semantics.

`ExpectedCatalogIdentity` can reject a worker whose rule source does not match
the caller's input source. Omitting it does not prove that the observation came
from the correct game version. A game adapter is responsible for validating the
installed game; an offline client is responsible for choosing and declaring its
own data source.

Structural validation is not a substitute for domain validation. The runtime
may report unsupported mechanisms, an unsimulatable state, or a censored
result; callers must not silently present those as reliable predictions.

## Versioning and compatibility

The first public API major is `1`, the input schema is `1`, and the first public
wire revision is `69`. Private wire revisions 68 and earlier are not part of the
compatibility promise. Worker versions are independent from LookingIN plugin
versions.

Within one public API major, existing methods, fields, and wire identifiers are
kept stable. New capabilities should be additive. Expanding an accepted version
range alone does not prove compatibility: old-client/new-worker regression tests
are required before publishing a new wire revision.

`sdk/public-api-v1.txt` guards public type/member signatures, while the protocol
baseline guards codec structure. Neither alone proves semantic compatibility;
defaults, errors, input interpretation, and rule changes still require behavior
regression tests.

The worker does not update itself over the network. A host or user selects a
trusted compatible runtime. Incompatible API majors should be rejected or kept
side-by-side rather than silently overwritten. Active sessions do not support
hot replacement.

## Player updates versus developer SDK

The player-facing GitHub Release keeps the historical four-file layout:

```text
LookingIN.dll
LookingIN.Compute.Worker.exe
LookingIN.Updater.exe
README.md
```

The two SDK DLLs are published separately to the Distribution repository's
`sdk/` directory. They are intentionally absent from the player Release and
signed update manifest. This preserves compatibility with existing automatic
updaters while giving third-party developers a stable, supported entry point.

## Regression entry points

```bash
dotnet run --project tests/LookingIN.Compute.Client.Verification -c Release
dotnet run --project tests/LookingIN.Compute.Verification -c Release
python3 tools/verify-compute-protocol.py --check
python3 tests/LookingIN.Compute.Protocol.Verification/test_verify_compute_protocol.py
```

The public API baseline is immutable after publication unless a compatibility
review explicitly approves a change. A managed build passing does not replace
Windows NativeAOT and real process-lifecycle verification.

