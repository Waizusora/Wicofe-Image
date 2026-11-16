# Wicofe System Image Architecture 0.1

**Status:** Draft proposal. This document defines image composition and validation responsibilities. It does not freeze a partition table, filesystem format, boot layout, or release image specification.

## 1. Purpose

Wicofe-Image transforms validated build artifacts and an explicit system manifest into reproducible filesystem trees and bootable disk or virtual-machine images. It owns image composition, not compilation of the operating system as a whole.

The image pipeline must be deterministic, inspectable, safe to run on a host, and capable of producing validation evidence alongside every artifact.

## 2. Repository ownership

| Repository | Canonical responsibility |
|---|---|
| Wicofe-Image | Image manifests, package selection for images, filesystem tree assembly, disk/VM image construction and image validation |
| Wicofe-Build | Whole-system dependency graph, compilation orchestration, build artifact manifest and handoff |
| WPM | Package format, repositories, package dependency resolution and package install/remove operations |
| ONWFS | ONWFS disk format, formatter, inspector and format-specific validation |
| Wicofe-Kernel | Kernel image and boot/runtime implementation |
| Wicofe-Installer | Deployment of an image/system payload onto a target device |
| Wicofe-Update | Whole-system update metadata, staging, activation and rollback |
| Wicofe-ThirdParty | Third-party provenance, license and compliance records |
| Wicofe-Tests | Cross-component image, boot, install and system test suites |

Wicofe-Image consumes outputs from Wicofe-Build and package inputs selected through explicit metadata. It must not become a second package manager, kernel build system, installer, updater, or filesystem-format authority.

## 3. Image artifact classes

Keep these artifact classes distinct:
- **Root filesystem tree:** staged directory tree with ownership, modes, links, timestamps and file contents represented according to the target filesystem capabilities.
- **Filesystem image:** a block image containing a specific filesystem, created by that filesystem's owner tooling.
- **Boot component artifact:** bootloader files, configuration, kernel image and other boot inputs with exact version and provenance.
- **Disk image:** partitioned container that references filesystem and boot artifacts under a versioned layout.
- **VM image:** a disk image or platform-specific artifact prepared for a declared emulator/hypervisor.
- **Release bundle:** published artifacts plus manifests, checksums, signatures/attestations and validation reports.

Do not call a root tree a bootable disk image. Record the artifact type explicitly.

## 4. Image manifest

A versioned image manifest should describe intent and exact inputs, not rely on ambient host state. It should include:
- schema version and image profile identifier;
- target architecture and CPU baseline;
- Wicofe ABI and system compatibility revisions;
- exact component artifact identifiers and hashes;
- package selection inputs and package versions;
- required boot components and configuration;
- filesystem type and feature profile;
- image layout profile reference, when formally specified;
- build-time options and deterministic metadata policy;
- output artifact types and expected validation stages;
- third-party component record references and license inventory.

Reject mutable branch names, unresolved package versions, duplicate component identities, missing hashes, incompatible architectures and unsupported feature combinations in release mode.

A manifest parser must reject unknown required fields and invalid schema versions. Optional extension fields must be namespaced or explicitly versioned so that older tools do not silently misinterpret them.

## 5. Artifact intake and trust

Accept artifacts only through an explicit input manifest from Wicofe-Build or an approved package/artifact source. Verify hashes, architecture, ABI compatibility, provenance, and required license records before staging.

Treat archive entries, filenames, symlinks, metadata, package manifests and filesystem contents as untrusted input. Reject path traversal, absolute paths, invalid path encodings, duplicate/conflicting paths, unsupported file types, unsafe hardlinks and symlinks escaping the staging root.

Do not execute scripts found inside imported artifacts during image composition unless a separately reviewed, sandboxed build step explicitly requires it.

## 6. Filesystem tree assembly

Tree generation must be deterministic:
- stable path ordering;
- explicit file ownership and permission policy;
- defined handling for timestamps and reproducible metadata;
- deterministic symlink and hardlink treatment;
- explicit device-node policy;
- no dependence on host umask, locale or timezone;
- collision detection across selected packages and system components.

The manifest must define which package/component owns each installed path. Resolve collisions through an explicit policy; never let input ordering silently choose the winner.

Host staging must preserve target metadata without requiring unsafe host privileges where possible. If privileged operations are necessary, isolate them and validate every destination.

## 7. Filesystem backends

Wicofe-Image should use the authoritative tool for each filesystem format:
- ONWFS images must be produced and checked using ONWFS-owned format tooling and the exact supported format revision.
- Other filesystems require their own declared formatter, feature profile and validation method.
- A filesystem feature must not be enabled unless the target kernel and required userspace tools support it.

ONWFS format 0.2.x remains a draft proposal, not a stable production format. Do not make it the default release filesystem or claim compatibility until the format is frozen, tooling exists, and conformance/fault-injection tests pass.

FAT32 and other filesystems with limited Unix metadata semantics require explicit ownership, permission and symlink policy. Do not imply they preserve native Unix semantics when they do not.

## 8. Disk and boot layout

Partition tables, boot partitions, firmware modes, bootloader installation, kernel placement and root filesystem discovery must be described by a separately versioned layout profile.

This architecture intentionally does not choose:
- GPT/MBR policy or partition GUID/type assignments;
- BIOS/UEFI support matrix;
- EFI System Partition layout;
- Limine configuration details;
- root filesystem identifiers or discovery protocol;
- encryption, swap or recovery partition policy;
- alignment and minimum image size.

These decisions must be coordinated with Wicofe-Kernel, bootloader integration, Wicofe-Installer, Wicofe-Recovery and Wicofe-Update. A layout profile must be rejected when its required boot or filesystem features are unsupported by the selected target.

## 9. Image construction pipeline

Proposed stages:

1. **Preflight:** validate manifest schema, target, source revisions, input availability and output destination.
2. **Verify inputs:** check hashes, signatures/attestations where available, architecture, ABI and provenance.
3. **Resolve composition:** produce a complete path/package ownership plan and reject collisions.
4. **Stage tree:** create a deterministic target filesystem tree in an isolated workspace.
5. **Validate tree:** inspect paths, metadata, links, required files, permissions and package ownership.
6. **Create filesystem artifact:** invoke the authoritative filesystem tooling and record format/features.
7. **Assemble disk/VM image:** apply a selected, versioned layout profile and boot artifacts.
8. **Inspect image:** independently parse partition, filesystem and boot structures; verify boundaries and hashes.
9. **Run tests:** invoke Wicofe-Tests contract, emulator boot and applicable hardware suites.
10. **Publish bundle:** emit immutable artifacts, checksums, manifest, provenance, license inventory and validation reports.

Each stage must have explicit inputs and outputs. Failed validation blocks publication.

## 10. Validation and evidence

Validation must be layered and reported separately:
- manifest/schema validation;
- artifact hash and provenance verification;
- filesystem tree policy validation;
- filesystem structural check;
- partition boundary and overlap checks;
- boot configuration and required-file inspection;
- emulator boot and system smoke tests;
- installer deployment tests;
- update/recovery compatibility tests;
- physical hardware tests where available.

A structurally valid image is not necessarily bootable. A successful emulator boot is not physical hardware validation. Never report a test as passed unless it was executed and its source/tool versions and logs are retained.

Use disposable images and virtual disks for destructive tests. Include malformed manifests, truncated inputs, overlapping partitions, corrupt filesystem metadata, archive traversal attempts and interrupted image generation in the negative test matrix.

## 11. Reproducibility and release metadata

The same manifest, input hashes, tools and configuration should produce byte-identical artifacts where the format and tooling permit. Normalize timestamps, ordering, locale, timezone, random seeds and archive metadata.

Every output should have:
- artifact ID/type and schema/profile version;
- architecture and ABI compatibility;
- manifest digest;
- input artifact hashes;
- tool and formatter versions;
- filesystem format and feature flags;
- source revisions and third-party record references;
- output hash and size;
- validation status and report references.

Keep reproducibility results distinct from functional validation. If a tool embeds unavoidable nondeterministic data, document the field and define a canonical comparison method.

## 12. Security and release integrity

Build images in isolated workspaces with bounded disk usage and explicit output paths. Validate all offsets, lengths and integer arithmetic before allocating or writing. Never write to a host block device as part of ordinary image generation.

Release signing must use a separately defined trust and key-management policy. Do not embed private signing keys in source repositories or CI logs. Verify signed metadata before publication and provide a documented key rotation and revocation model.

## 13. Licensing

Original Wicofe-Image code targets MPL-2.0. Files included in a system image retain their individual licenses and notices. Generate a release license inventory from exact selected artifacts and their transitive dependencies; do not assume the image as a whole is uniformly MPL-2.0.

## 14. Testing requirements

- Manifest schema and backward/forward compatibility tests.
- Artifact identity, hash and architecture mismatch tests.
- Archive/path traversal, symlink and hardlink security tests.
- Package path collision and ownership policy tests.
- Deterministic tree generation and independent rebuild comparison.
- Filesystem image creation and independent inspection.
- Partition boundary, alignment and overlap tests.
- Boot artifact/configuration consistency tests.
- Corrupt/truncated image rejection tests.
- Interrupted generation and cleanup tests.
- Emulator, installer, update and recovery integration tests.
- Release provenance, license inventory and signature verification.

## 15. Open decisions

- Manifest serialization and schema evolution policy.
- Package input contract and exact relationship to WPM repository metadata.
- Filesystem tree staging implementation.
- ONWFS final format version and tooling readiness.
- Supported filesystems and feature profiles.
- GPT/MBR and firmware/boot support matrix.
- Limine and boot artifact handoff contract.
- Image compression and sparse-file policy.
- Signing, attestation and key rotation policy.
- Release channels, artifact naming and retention.

## 16. Completion criteria

A production image pipeline requires a versioned manifest, immutable verified inputs, deterministic tree assembly, authoritative filesystem tooling, a frozen boot/layout profile, independent structural inspection, passing architecture-specific boot/integration tests, complete provenance/license metadata and a documented release-signing process. Until then, this remains a draft architecture.
