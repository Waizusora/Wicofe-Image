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

## Reproducibility

Record exact source revisions, toolchain versions, package inputs, build configuration, and artifact hashes. Avoid unpinned network dependencies in release builds.

## Status

Initial repository baseline; no official Wicofe image format or release image is claimed.

## License

Original project code targets MPL-2.0; image contents retain the licenses of their included components.
