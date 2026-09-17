# LookingIN Distribution

[简体中文](README.md) | **English**

This is the official public distribution repository for LookingIN. It serves
two audiences:

- **Players** download and automatically update LookingIN through GitHub Releases.
- **Third-party developers** use the public Compute SDK under `sdk/` to call the maintained computation runtime through a supported interface.

> [!IMPORTANT]
> This is a **distribution repository, not the complete source repository**.
> The LookingIN plugin and Compute Worker remain proprietary. The public SDK is
> separately MIT-licensed. Publicly downloadable binaries do not make the whole
> project open source.

## For players

Install LookingIN from GitHub Releases. The player package deliberately keeps
the historical four-file layout:

```text
LookingIN.dll
LookingIN.Compute.Worker.exe
LookingIN.Updater.exe
README.md
```

Existing automatic updaters continue to recognize this layout. Before changing
an installation, the update flow verifies the signed update manifest and the
SHA-256 digest of the downloaded package.

The DLLs under `sdk/` are **not player installation files**. Do not copy them
into `BepInEx/plugins/LookingIN`, and do not manually launch the Worker or
Updater executables.

## Third-party development / Compute SDK

LookingIN maintains the computation engine as an independent local runtime.
Third-party developers do not need to reference or modify the official
`LookingIN.dll`. The supported integration model is:

```text
Your BepInEx DLL / desktop application
        │
        ├─ LookingIN.Compute.Client.dll
        └─ LookingIN.Compute.Contracts.dll
        │
        ▼
LookingIN.Compute.Worker.exe
```

The public SDK handles capability discovery, worker launch, IPC, cancellation,
error mapping, and lifecycle. The Worker owns rule projection, simulation, and
search. A public API does not make the Worker implementation, Simulator, or
embedded Catalog open source.

Developer entry points:

- [Compute SDK 中文文档](sdk/README.md)
- [Compute SDK English documentation](sdk/README.en.md)
- [SDK MIT License](sdk/LICENSE.txt)

Before the first SDK-enabled formal release, `sdk/` may contain documentation
and licensing only. Formal releases synchronize the matching
`LookingIN.Compute.Client.dll` and `LookingIN.Compute.Contracts.dll` into that
directory. The SDK DLLs are intentionally not GitHub Release assets and are not
part of the player automatic-update ZIP.

## Versions and compatibility

The player version, Worker version, and public SDK/API version are separate
version dimensions. Third-party clients should use a matching Client/Contracts
pair and rely on SDK capability discovery before connecting to a Worker. Do not
depend on internal types in `LookingIN.dll`, reflection names, or implementation
details that may be changed by Obfuscar.

Distribution version tags identify the SDK snapshot published together with the
corresponding formal LookingIN release, making historical compatible versions
easy to locate.

## Repository contents

The Git branch keeps only the public material that should remain directly
browsable:

- `README.md` / `README.en.md`: repository overview and language entry points;
- `sdk/`: developer SDK binaries, documentation, and separate license;
- [`LICENSE.txt`](LICENSE.txt): proprietary LookingIN EULA;
- [`THIRD-PARTY-NOTICES.txt`](THIRD-PARTY-NOTICES.txt): third-party notices.

Player ZIPs, signed update manifests, checksums, and formal release notes are
maintained as GitHub Release assets instead of being duplicated on the Git
branch.

## License boundary

The LookingIN plugin, Worker, Simulator, embedded rules/catalog, and other
components not explicitly opened are distributed under the proprietary terms in
[`LICENSE.txt`](LICENSE.txt).

`LookingIN.Compute.Client`, `LookingIN.Compute.Contracts`, and explicitly marked
SDK material are provided under the MIT License in
[`sdk/LICENSE.txt`](sdk/LICENSE.txt). The SDK license does not grant rights to
the proprietary Worker implementation, game data, LookingIN branding, or other
components outside that scope.

## Update security

Automatic updates verify a signed manifest and the downloaded archive's
SHA-256. The release pipeline also validates the archive file whitelist. If you
are a player, prefer official Releases and the built-in update path rather than
mixing Worker or SDK binaries from untrusted sources.

## Project relationship

LookingIN is an independent third-party project. It is not affiliated with,
endorsed by, sponsored by, or supported by Tempo, The Bazaar, Unity, Microsoft,
or their respective owners. Third-party names and trademarks belong to their
respective owners.

