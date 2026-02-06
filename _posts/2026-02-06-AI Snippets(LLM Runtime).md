---
title: "[Snippets]LLM Runtime"
categories: ai
tags: [Snippets, AI]
layout: single
share: false
---

## What is llama.cpp?

**llama.cpp** is a C/C++-based **inference engine** designed to run LLMs
locally with maximum simplicity and performance.

-   Dedicated runtime for **GGUF**
-   Extreme CPU optimization
-   Usable performance even without GPU
-   Direct GPU backends: **CUDA / Metal / Vulkan**

---

## GGUF

**GGUF** is the model format designed specifically for **llama.cpp**.\
It includes **quantization metadata**, enabling efficient inference on
limited hardware.

---

## CURL in llama.cpp

In **llama.cpp**, `CURL` usually refers to **libcurl** (client URL transfer library).  
It is **not part of the AI model itself**, but an optional external dependency used for networking.

### Common Uses
- Communicating with remote servers
- Downloading model files
- Calling OpenAI-compatible APIs
- Streaming responses
- External API calls in tool/function calling (depending on build options)

CURL is optional and can be enabled/disabled via **CMake build options**.

**Reference Post**: [[Snippets]Build & Toolchain](https://dongheon3313.github.io/ai/AI-Snippets(Build-&-Toolchain)/)  

### Why use CURL?
- Supports multiple protocols (HTTP / HTTPS / FTP, etc.)
- Handles streaming / chunked responses
- Supports authentication, headers, and proxy
- Cross-platform (Windows / Linux / macOS)

**In short:** It avoids implementing network communication from scratch.
