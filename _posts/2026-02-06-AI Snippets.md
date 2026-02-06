---
title: "[Snippets]AI Snippets"
categories: ai
tags: [Snippets, AI]
layout: single
share: false
---

## Attention

**Attention**: A mechanism that computes how strongly each token in a sequence relates to other tokens in the same sequence.

---

## FlashAttention

**FlashAttention**: An algorithm that drastically reduces memory reads/writes during attention computation.  
Modern GPUs have extremely fast compute, but memory bandwidth is relatively slower — FlashAttention was designed to remove this bottleneck.

### Core Principles

**1. Tiling**  
Split data into small tiles and process them inside fast on-chip GPU memory (SRAM/shared memory).

**2. Recomputation**  
Instead of storing large intermediate results in slow memory, recompute them when needed.  

---

## CUDA

**CUDA** is a parallel computing platform and programming model developed by NVIDIA.  
It enables efficient execution of massively parallel workloads on NVIDIA GPUs.

---

## PyTorch and CUDA

**PyTorch** is a deep learning framework that uses **CUDA as its backend** to accelerate tensor operations on NVIDIA GPUs.

---

## torch.Tensor vs numpy.ndarray

### When `torch.Tensor` is better
- When continuing computation on GPU
- When chaining PyTorch operations

### When `numpy.ndarray` is better
- FAISS indexing
- Saving to disk

**For simple vector search DB:**  
In a **RAG + FAISS** setup, `numpy` is typically the right choice.

**FAISS (Facebook AI Similarity Search)**:  
A library for efficient similarity search and clustering of large-scale high-dimensional vectors.

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

### Why use CURL?
- Supports multiple protocols (HTTP / HTTPS / FTP, etc.)
- Handles streaming / chunked responses
- Supports authentication, headers, and proxy
- Cross-platform (Windows / Linux / macOS)

**In short:** It avoids implementing network communication from scratch.
