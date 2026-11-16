# OSynit Image Integration 0.1

- **Status:** Proposed image integration
- **Owner:** Wicofe-Image
- **Component authority:** [OSynit](https://github.com/Waizusora/Osynit)
- **Date:** 2026-11-16

## 1. Purpose

Define the root-filesystem and image-manifest requirements for packaging OSynit. This does not freeze a partition layout, bootloader configuration, filesystem format, or claim a bootable release image.

## 2. Required system payload

A Wicofe system profile that selects OSynit as init must identify:
- the OSynit executable artifact, architecture, ABI compatibility, source revision and hash;
- the init executable path and boot handoff configuration;
- the selected OSynit configuration profile;
- required service unit and target definitions;
- the minimum runtime directory and filesystem prerequisites;
- the expected platform capability profile.

The executable name is `osynit`; the project name is **OSynit**. The proposed installed path is `/usr/sbin/osynit`, pending root filesystem policy.

## 3. Configuration and runtime paths

The proposed configuration namespace is `/etc/osynit/`, including `osynit.conf`, `units/`, and `targets/`. Optional module configuration must not be required by Core Init.

The proposed runtime namespace is `/run/osynit/`, including the manager socket and state/job/service/lock metadata. Runtime directories are ephemeral and must be initialized with restrictive ownership and permissions by the appropriate early-userspace component.

Persistent logs under `/var/log/osynit/` are optional and must not be a prerequisite for reaching a safe boot state.

The root filesystem profile must validate the availability and semantics of `/usr`, `/etc`, `/run`, and `/var`; do not assume a merged-/usr layout or tmpfs behavior without a separate system layout decision.

## 4. Init handoff

The boot manifest must point to the selected OSynit executable using the kernel's documented init handoff contract. The kernel starts it as PID 1; the image builder must not embed service lifecycle policy in bootloader configuration.

If OSynit is absent, hash-invalid, ABI-incompatible, or lacks required platform capabilities, the image must fail validation rather than silently selecting an unrelated init implementation.

## 5. External services and optional modules

The image manifest separately selects external daemons and their service unit definitions. Network, device, storage, user/session, time, audio, graphics, and desktop behavior remain outside OSynit's mandatory core.

Optional mount/socket/timer/logging/resource/security/watchdog modules are included only when explicitly selected and compatible. Their absence must not block Core Init.

## 6. Validation

Image validation must check executable presence/hash/architecture, init handoff path, configuration syntax/schema version, unit references, target dependencies, permissions, runtime path policy, and consistency between selected optional modules and configured units.

A structurally valid image is not necessarily bootable. Native/emulator boot evidence must be recorded separately for each architecture and image profile.
