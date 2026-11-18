# Wicofe-Image

System image composition and release artifact definitions for Wicofe OS.

## Scope

- Image manifests and package selection.
- Bootable disk and virtual-machine image assembly.
- Reproducible filesystem tree generation.
- Image metadata, checksums, and release artifact layout.
- Validation of boot, partition, and filesystem structures.

## Boundaries

Wicofe-Build owns build orchestration and toolchain management. WPM owns package management. Wicofe-Installer deploys images or system payloads. Wicofe-Update manages installed-system updates.

## Frenit root-filesystem integration

[Frenit](https://github.com/Waizusora/Frenit) is the planned canonical init executable for Wicofe images. Wicofe-Image owns root-filesystem composition and manifest validation; it does not implement Frenit or define kernel process/ABI behavior. A future image manifest must declare the Frenit artifact revision/hash, target architecture, runtime dependencies, configuration and ABI compatibility. Do not invent an executable path or select Frenit as PID 1 until the cross-component gates in [WIC-SPEC-BOOT-FRENIT-001](https://github.com/Waizusora/Wicofe-Specs/blob/main/boot/FRENIT_INTEGRATION_CONTRACT_0.1.md) are accepted and verified.

## Reproducibility

Record exact source revisions, toolchain versions, package inputs, build configuration, and artifact hashes. Avoid unpinned network dependencies in release builds.

## Status

Initial repository baseline; no official Wicofe image format or release image is claimed.

## License

Original project code targets MPL-2.0; image contents retain the licenses of their included components.

## Documentation

See [docs/INDEX.md](docs/INDEX.md) for this repository's technical documentation. The [central repository documentation map](https://github.com/Waizusora/Wicofe-Docs/blob/main/REPOSITORY_DOCUMENTATION_MAP.md) identifies canonical ownership and cross-repository boundaries.
