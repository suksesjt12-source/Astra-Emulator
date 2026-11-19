# Astra Emulator

Astra Emulator is an Android-focused emulator development fork based on the Lemon Project source tree.

## Project goals

- Preserve the upstream emulator architecture and build system.
- Establish a reproducible Android build pipeline.
- Add carefully scoped ARM Mali Vulkan compatibility work, especially for Mali-G52/G57 devices.
- Validate rendering correctness and stability before enabling performance-specific changes.

## Source import status

The upstream Lemon Project source archive supplied in this conversation contains 4,385 entries (about 54.86 MiB when extracted). The archive has been inspected, but its contents have **not** yet been imported into this Git repository. This README is project setup documentation, not evidence of a completed source import.

## Upstream source

- Project: Lemon Project
- Supplied archive: `Lemon-Project-main.zip`

## Development principles

1. Keep upstream changes isolated and reviewable.
2. Prefer Vulkan capability checks over broad vendor-name assumptions.
3. Disable optional Vulkan features only when a reproducible compatibility issue justifies it.
4. Keep performance tuning separate from correctness and stability fixes.
5. Require Android build and runtime validation before marking a change complete.

## Initial target hardware

Android devices using MediaTek Helio platforms and ARM Mali GPUs, including Mali-G57-class hardware. This is a testing target, not a claim of confirmed compatibility.

## Build status

No APK has been built or tested from this repository yet. Android build instructions and CI must be validated after the source import.
