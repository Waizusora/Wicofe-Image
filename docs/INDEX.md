# Image Documentation Index

This directory documents system image composition and release artifact definitions.

## Architecture
- [System image architecture 0.1](IMAGE_SYSTEM_ARCHITECTURE_0.1.md) — draft design for manifests, artifact intake, filesystem trees, disk/boot layout boundaries, validation and reproducible release bundles.
- [Frenit image integration 0.1](FRENIT_IMAGE_INTEGRATION_0.1.md) — init artifact, ABI compatibility, root-filesystem manifest and boot-handoff gates.

## Whole-system product direction
- [Wicofe system delivery and installation direction 0.1](https://github.com/Waizusora/Wicofe-Doc/blob/main/SYSTEM_DELIVERY_AND_INSTALLATION_DIRECTION_0.1.md) — planned Delivery Release model and ordinary versus urgent security artifact delivery.

## Scope
- Image manifests and package selection.
- Bootable disk and virtual-machine image assembly.
- Reproducible filesystem tree generation.
- Image metadata, hashes, partition/boot layout, and validation.

## Ownership
[Wicofe-Build](https://github.com/Waizusora/Wicofe-Build) owns build orchestration and toolchains. [WPM](https://github.com/Waizusora/WPM) owns package management. [ONWFS](https://github.com/Waizusora/ONWFS) owns its disk format and format-specific tools. [Wicofe-Installer](https://github.com/Waizusora/Wicofe-Installer) deploys images. [Wicofe-Update](https://github.com/Waizusora/Wicofe-Update) manages installed-system updates.

## Reproducibility
Record exact source revisions, toolchain versions, package inputs, configuration, and artifact hashes. Do not claim an official release image until its manifest and validation evidence are published. ONWFS 0.2.x remains a draft proposal and must not be treated as a stable production format.

- [Central documentation index](https://github.com/Waizusora/Wicofe-Doc/blob/main/INDEX.md)
- [Repository documentation map](https://github.com/Waizusora/Wicofe-Doc/blob/main/REPOSITORY_DOCUMENTATION_MAP.md)
