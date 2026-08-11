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

## Usage

### 1. Manual Trigger
1. Go to the **Actions** tab in this repository.
2. Select **Build and Release ONNX Runtime WebGPU**.
3. Click **Run workflow**.

### 2. Tagged Release
Push a git tag starting with `v` to trigger a build and publish a new release:
```bash
git tag v1.0.0
git push origin v1.0.0
```
