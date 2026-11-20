# Astra Helio CPU build profile

Astra provides an opt-in CPU code-generation preset for Android ARM64 devices whose complete CPU cluster supports the selected instruction baseline.

## Helio optimized build

Run the standard Android release build with:

```sh
ASTRA_HELIO_OPTIMIZED=true .ci/android/build.sh -t standard -b Release
```

This selects the existing ARM64 `YUZU_BUILD_PRESET=optimized` setting, which adds `-march=armv8.2-a+lse+rcpc`. It is optional; the default build remains on the generic ARM64 target.

MediaTek lists the Helio G100 as two Arm Cortex-A76 cores and six Cortex-A55 cores, with a Mali-G57-class GPU. Verify the exact SoC and CPU implementation before distributing an optimized APK to other device families. The optimized build is not a universal ARM64 APK. See the [MediaTek Helio G100 specification](https://www.mediatek.com/products/smartphones/mediatek-helio-g100), the [Cortex-A76 information](https://support.arm.com/compute-ip/cortex-a76), and the [Cortex-A55 information](https://support.arm.com/compute-ip/cortex-a55).

The Android app's native ABI filter remains `arm64-v8a`. The profile does not change CPU frequencies, voltage, thermal policy, or kernel settings.

## Astra Android identity

The standalone Astra variant installs with application ID `dev.astra.emulator` and launcher name `Astra Emulator`. It does not use AnTuTu's `com.antutu.ABenchMark` application ID. The internal Gradle flavor name remains `spoofed` for compatibility with the upstream native updater asset suffix; the build script exposes it publicly as `astra`.

Build the standalone flavor with:

```sh
.ci/android/build.sh -t astra -b Release
```

The resulting development APK is also copied to a filename beginning with `Astra-` and ending in `spoofed.apk`, which keeps the existing in-app updater asset lookup working.

## Helio in-app performance preset

The Android Quick Settings performance tile now cycles through Speed, Normal, Quality and Helio. The Helio preset applies:

- 0.75x render resolution.
- Nearest-neighbor scaling.
- No anti-aliasing and low GPU accuracy.
- Asynchronous shader work and asynchronous GPU emulation.
- Disk shader cache enabled.

This is a GPU-load-oriented configuration, not an overclock. Image quality is reduced compared with Normal/Quality modes. It can help when rendering load is the bottleneck; it cannot speed up CPU-bound workloads or guarantee a fixed FPS. The existing Adaptive Performance feature may still switch to Speed when thermal or performance triggers fire.

## Build validation and metadata

The Android GitHub Actions workflow:

- Runs a preflight check for shell syntax and the Android ARM64/Gradle/CMake option wiring.
- Supports a manual `astra_helio_optimized` input to build the optimized variant.
- Runs the regular Android NDK build after preflight.
- Uploads `ASTRA-BUILD-PROFILE.txt` beside the APK/AAB artifact, recording the commit, ABI, flavor, build type, and CPU optimization choice.

## Device validation

For a meaningful comparison, use the same game, driver, resolution, scene, and power conditions for the generic and optimized builds. Record frame-time percentiles, peak RAM, device temperature, and whether the game completes a repeatable 20–30 minute session.

A successful CI build confirms compilation only. It does not prove a game is playable or that FPS, stability, or temperature improves on a physical device.
