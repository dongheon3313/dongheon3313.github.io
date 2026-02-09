---
title: "[Basics]Inference Theory"
categories: ai
tags: [Basic, AI]
layout: single
share: false
---

## Attention

**Attention**: A mechanism that computes how strongly each token in a sequence relates to other tokens in the same sequence.

---

## From Embedding to Contextual Meaning

**Embedding** captures the *intrinsic meaning* of a word, while **Attention layers** transform it into a *context-aware meaning*.

```
Token → Embedding (static meaning)
      → Attention layers (contextual meaning)
      → Contextual vector (true meaning in context)
```

### Example

**0) Starting sentence**

> "The bank is near the river"

---

**1) Token → Embedding (no context yet)**
At this stage, each token only contains its independent lexical meaning.

* bank → [0.21, -0.44, ...]
* river → [0.55, 0.11, ...]

The vector for **bank** does not yet know whether it refers to a financial institution or a riverside.

---

**2) Self-Attention (Layer 1) → Attention layer output**
Now tokens begin to interact. The model computes how strongly each token relates to others.

* Attention from **bank → river** increases
* The vector of **bank** starts incorporating information related to *river*
* bank → *a vector mixed with river-related meaning*

This is where context begins to shape interpretation.

---

**3) Repeated across multiple layers → Contextual vector (final meaning)**
As attention layers stack deeper, the representation becomes progressively more refined.

* **Early layers** → capture syntax and local structure
* **Middle layers** → learn semantic relationships
* **Later layers** → form higher-level and abstract meaning

By the final layer, **bank**

---

## FlashAttention

**FlashAttention**: An algorithm that drastically reduces memory reads/writes during attention computation.  
Modern GPUs have extremely fast compute, but memory bandwidth is relatively slower — FlashAttention was designed to remove this bottleneck.

### Core Principles

**1. Tiling**  
Split data into small tiles and process them inside fast on-chip GPU memory (SRAM/shared memory).

**2. Recomputation**  
Instead of storing large intermediate results in slow memory, recompute them when needed.  
