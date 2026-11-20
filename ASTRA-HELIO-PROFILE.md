# Astra Helio and ARM Mali profile

This profile is intended for Android ARM64 devices with big.LITTLE CPU clusters and ARM Mali Vulkan drivers. It separates two independent changes.

## 1. Mali Vulkan compatibility profile

On Android ARM64 builds, `ASTRA_MALI_COMPATIBILITY_PROFILE` defaults to OFF. Enable it only when investigating a Mali Vulkan compatibility issue. At runtime it applies only when both the Vulkan vendor ID is ARM (`0x13B5`) and the driver ID is `VK_DRIVER_ID_ARM_PROPRIETARY`.

The profile disables these optional Vulkan feature paths for that driver:

- `VK_EXT_extended_dynamic_state2`
- `VK_EXT_extended_dynamic_state3`
- `VK_EXT_provoking_vertex`
- `VK_KHR_robustness2` or `VK_EXT_robustness2`

The emulator then uses its existing fallback paths. This is a conservative compatibility change; it is **not** a measured FPS improvement, and it does not affect Adreno, Turnip, Intel, or NVIDIA drivers. The Vulkan startup log includes GPU name, driver name/info, vendor ID and driver ID. A log entry beginning with `Astra Mali profile:` confirms that the optional fallback path was selected.

To enable the profile in a custom CMake build, pass:

```text
-DASTRA_MALI_COMPATIBILITY_PROFILE=ON
```

## 2. Optional Helio CPU code-generation preset

The Android Gradle build accepts `-PastraHelioOptimized=true`. The build script exposes it as an environment variable:

```sh
ASTRA_HELIO_OPTIMIZED=true .ci/android/build.sh -t standard -b Release

# Only when testing the Mali driver compatibility fallback:
ASTRA_MALI_COMPATIBILITY_PROFILE=true .ci/android/build.sh -t standard -b Release
```

This selects the existing ARM64 `YUZU_BUILD_PRESET=optimized` preset, which compiles with `-march=armv8.2-a+lse+rcpc`. It is opt-in and must only be used for devices whose complete CPU cluster supports that instruction baseline. It is not a universal Android ARM64 APK. The default build remains generic.

The preset is intended for SoCs whose CPU topology and ISA have been verified, such as Cortex-A76/A55-based variants. MediaTek lists the Helio G100 as two Cortex-A76 cores plus six Cortex-A55 cores and an Arm Mali-G57-class GPU. See the [MediaTek Helio G100 specification](https://www.mediatek.com/products/smartphones/mediatek-helio-g100) and Arm's [Cortex-A76](https://support.arm.com/compute-ip/cortex-a76) and [Cortex-A55](https://support.arm.com/compute-ip/cortex-a55) documentation. Do not distribute an optimized build to unrelated ARM64 devices without checking their ISA support.

## Validation checklist

1. Confirm the device Vulkan driver reports ARM proprietary and the log contains the Astra Mali profile message.
2. Confirm games still reach the renderer and fallback descriptor handling is correct.
3. Compare frame-time percentiles, not only average FPS, with the generic build using the same game, scene, resolution, and driver.
4. Record peak RAM and device temperature over a repeatable 20–30 minute session.
5. Test a non-Mali Android GPU to confirm the driver-specific path is not activated.

No APK build or physical-device performance result is claimed by this source change. Pull requests run a shell/profile preflight followed by the standard Android build. Manual workflow dispatch exposes independent switches for the Helio CPU preset and the optional Mali compatibility profile.
