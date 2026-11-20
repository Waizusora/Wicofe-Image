# Frenit Image Integration 0.1

- **Status:** Proposed image integration; no Frenit-enabled native boot image is verified.
- **Owner:** Wicofe-Image for root-filesystem composition and manifest validation.
- **Component authority:** [Frenit — Free Init](https://github.com/Waizusora/Frenit).
- **Cross-component contract:** [WIC-SPEC-BOOT-FRENIT-001](https://github.com/Waizusora/Wicofe-Spec/blob/main/boot/FRENIT_INTEGRATION_CONTRACT_0.1.md).
- **Date:** 2026-11-18

## 1. Purpose

Define root-filesystem and image-manifest requirements for packaging Frenit. This does not freeze a partition layout, bootloader configuration, filesystem format, executable path or claim a bootable release image.

## 2. Required system payload

A future system profile that selects Frenit must identify:

- the exact Frenit artifact revision, hash, architecture and ABI compatibility;
- its runtime dependencies and required platform capabilities;
- the root-filesystem destination selected by an accepted filesystem layout policy;
- configuration and service metadata using versions defined by Frenit;
- the kernel/init handoff profile and required kernel/ABI capability baseline.

The Cargo package and executable are named `frenit`. Do not assume `/usr/sbin/frenit` or any other installed path until the root-filesystem layout is specified.

## 3. Configuration and runtime paths

Configuration names, service-unit schema, runtime directories, IPC endpoints and log paths are owned by Frenit and must be taken from its canonical accepted specification when available. Do not copy a previous init proposal's paths or schemas into the image manifest.

The image must declare which configuration and service metadata are installed and validate their schema/version against the exact Frenit artifact. Runtime-only directories must be created with restrictive ownership and permissions by the component that owns their initialization.

## 4. Init handoff

The current kernel does not yet implement a verified Frenit PID 1 handoff. Image manifests must not enable an init boot target or claim that the kernel will launch Frenit until the kernel executable handoff and ABI/process contracts are implemented and covered by native QEMU conformance tests.

If the Frenit artifact is absent, hash-invalid, architecture-incompatible or ABI-incompatible, image validation must fail rather than silently selecting an unrelated init implementation.

## 5. External services

The image manifest selects external daemons independently from Frenit. Network, device, storage, user/session, time, audio, graphics and desktop behavior remain owned by their respective service/subsystem repositories. Frenit owns their lifecycle policy, not their domain implementations.

## 6. Validation

Image validation must check artifact presence/hash/architecture, runtime dependency closure, ABI compatibility, configuration schema, service references, permissions and consistency with the selected kernel capability profile.

A structurally valid image is not necessarily bootable. Native/emulator boot evidence must be recorded separately for each architecture and image profile.
