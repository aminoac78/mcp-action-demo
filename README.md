# MCP Action Demo - ONNX Runtime WebGPU Builder

This repository demonstrates automated cross-compilation and releasing of **ONNX Runtime with WebGPU Execution Provider (EP)** for **Linux ARM64** and **Android ARM64-v8a** using GitHub Actions.

## Features

- **Linux ARM64 Build**: Automatically compiles ONNX Runtime with WebGPU on native ARM64 runners (`ubuntu-24.04-arm`).
- **Android ARM64-v8a Build**: Cross-compiles ONNX Runtime for Android using Snapdragon Android Toolchain container (`ghcr.io/snapdragon-toolchain/arm64-android:v0.7`).
- **Automated Releases**: Automatically packages shared libraries (`.so`, `.a`) and core headers into tarballs and publishes them to GitHub Releases when a version tag (`v*`) is pushed.

---

## Workflow Overview

The GitHub Actions workflow `.github/workflows/build-release.yml` performs the following steps:
1. **Linux ARM64 Job**: Clones ONNX Runtime, builds with `--use_webgpu`, packs headers and libraries.
2. **Android ARM64 Job**: Uses the Android NDK container, configures CMake with Ninja, builds with `--use_webgpu --android`, packs artifacts.
3. **Release Job**: Collects all built artifacts and creates/updates a GitHub Release.

---

## Release Artifacts & Usage Guide

Each Release provides two pre-compiled tarball packages:
- `onnxruntime-linux-arm64-webgpu.tar.gz` (for Linux AArch64 / ARM64)
- `onnxruntime-android-arm64v8a-webgpu.tar.gz` (for Android ARM64-v8a)

Both packages contain:
- `include/`: ONNX Runtime session and WebGPU provider headers.
- `lib/`: Compiled shared libraries (`.so`) and static libraries (`.a`).

### 1. Using in Linux ARM64 C++ Projects

1. Download and extract the Linux tarball in your project directory:
   ```bash
   tar -xzf onnxruntime-linux-arm64-webgpu.tar.gz -d third_party/onnxruntime
   ```
2. Configure your `CMakeLists.txt`:
   ```cmake
   include_directories(${CMAKE_CURRENT_SOURCE_DIR}/third_party/onnxruntime/include)
   link_directories(${CMAKE_CURRENT_SOURCE_DIR}/third_party/onnxruntime/lib)

   add_executable(my_app main.cpp)
   target_link_libraries(my_app PRIVATE onnxruntime)
   ```

### 2. Using in Android ARM64-v8a Projects (NDK / JNI)

1. Download and extract the Android tarball:
   ```bash
   tar -xzf onnxruntime-android-arm64v8a-webgpu.tar.gz -d third_party/onnxruntime-android
   ```
2. Place the `.so` libraries into your Android Studio project's JNI libs directory (`app/src/main/jniLibs/arm64-v8a/`).
3. Set up include paths in your app's `CMakeLists.txt`:
   ```cmake
   include_directories(${CMAKE_CURRENT_SOURCE_DIR}/third_party/onnxruntime-android/include)

   add_library(native-lib SHARED native-lib.cpp)
   target_link_libraries(native-lib PRIVATE 
       android
       log
       ${CMAKE_CURRENT_SOURCE_DIR}/third_party/onnxruntime-android/lib/libonnxruntime.so
   )
   ```

---

## Building & Releasing

### Manual Trigger
1. Go to the **Actions** tab.
2. Select **Build and Release ONNX Runtime WebGPU**.
3. Click **Run workflow**.

### Tagged Release
Push a git tag starting with `v` to trigger a build and publish a new release automatically:
```bash
git tag v1.0.0
git push origin v1.0.0
```
