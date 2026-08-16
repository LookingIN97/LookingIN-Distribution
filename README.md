# LookingIN Distribution

Official Windows binary releases and signed automatic-update manifests for
LookingIN.

The source code and immutable internal archives are maintained separately.
This repository is the curated public installation channel: release notes,
installable artifacts, checksums, and signed manifests are attached directly
to GitHub Releases instead of being duplicated on the Git branch.

Automatic updates verify both the signed release manifest and the SHA-256
digest of the downloaded archive before changing an installation.

## License

LookingIN is proprietary, closed-source software. Downloading, installing, or
using LookingIN is subject to the
[LookingIN Proprietary End User License Agreement](LICENSE.txt). The public
availability of release binaries does not make LookingIN open-source software.

[Third-party software notices](THIRD-PARTY-NOTICES.txt), including the complete
.NET Runtime notices for the self-contained NativeAOT executables, are provided
separately. Future GitHub Releases include both legal documents as standalone
assets; their full text is also appended to the packaged README for compatibility
with existing automatic updaters.
