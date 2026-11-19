# Astra Emulator — porting notes

This repository starts from the public Lemon-Project source baseline.

- Upstream mirror: https://github.com/Ghael-V/Lemon-Project
- Upstream branch imported: main (snapshot fetched 2026-11-19)
- This is not guaranteed to be a byte-for-byte copy of `Lemon-Project-main.zip`; spot checks found matching `CMakeLists.txt` and Android build workflow, but a differing `src/video_core/vulkan_common/vulkan_device.cpp`.
- Upstream copyright and license files are retained.
- Astra-specific Mali/Helio G100 changes must be implemented and tested in separate commits.
- No Android APK build or device validation is claimed by this import step.

Keep upstream license headers and notices intact when modifying source files.
