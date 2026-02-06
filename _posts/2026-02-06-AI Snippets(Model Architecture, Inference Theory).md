---
title: "[Snippets]AI Snippets: Model Architecture, Inference Theory"
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
