---
title: "[Basics]Build & Toolchain"
categories: ai
tags: [Basic, AI]
layout: single
share: false
---

## Building llama.cpp with CMake (Visual Studio Generator)

To use **CMake on Windows**, the **Visual Studio C++ build tools** must
be installed.

Example build:

```
cmake -B build -S . -G "Visual Studio 17 2022" -DLLAMA_CUDA=ON -DLLAMA_CURL=OFF -DCMAKE_CUDA_ARCHITECTURES=89
cmake --build build --config Release
```

However, **building through the Visual Studio generator is not
recommended for best performance**.

### Recommended: Ninja Build (llama.cpp + CUDA)

For CUDA builds, **Ninja is usually faster and more reliable than Visual
Studio**.

Run in:\
**x64 Native Tools Command Prompt for VS 2022**

```
cmake -B build -S . -G Ninja -DLLAMA_CUDA=ON -DLLAMA_CURL=OFF -DCMAKE_CUDA_ARCHITECTURES=89
cmake --build build --config Release
```
