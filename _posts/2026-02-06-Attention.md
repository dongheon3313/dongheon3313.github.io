---
title: "[Basics]Attention"
categories: ai
tags: [Basic, AI]
layout: single
share: false
---

## Definition

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

**1) Token → Embedding (no context yet)**
At this stage, each token only contains its independent lexical meaning.

* bank → [0.21, -0.44, ...]
* river → [0.55, 0.11, ...]

The vector for **bank** does not yet know whether it refers to a financial institution or a riverside.

**2) Self-Attention (Layer 1) → Attention layer output**
Now tokens begin to interact. The model computes how strongly each token relates to others.

* Attention from **bank → river** increases
* The vector of **bank** starts incorporating information related to *river*
* bank → *a vector mixed with river-related meaning*

This is where context begins to shape interpretation.

**3) Repeated across multiple layers → Contextual vector (final meaning)**
As attention layers stack deeper, the representation becomes progressively more refined.

* **Early layers** → capture syntax and local structure
* **Middle layers** → learn semantic relationships
* **Later layers** → form higher-level and abstract meaning

By the final layer, **bank**

**Related Post**: [[Basics]The Emergence of Meaning in Transformer Embeddings](https://dongheon3313.github.io/ai/The-Emergence-of-Meaning-in-Transformer-Embeddings/)

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

## Multi-Head Attention

Using only a single self-attention mechanism allows the model to capture **one dominant type of relationship** at a time — for example, syntax, semantics, or long-range dependency.
However, natural language contains **multiple relationships simultaneously**, and understanding requires modeling them together.

These relationships can be viewed in different ways:

* **Syntactic relationships** → subject–verb agreement, grammatical structure
* **Semantic relationships** → synonyms, similar concepts, related meanings
* **Coreference** → resolving references such as *“it” → “animal”*
* **Long-range dependencies** → connections between distant parts of a sentence

To capture these diverse patterns, Transformers use **multiple attention mechanisms in parallel**, known as **Multi-Head Attention**.

### Different Heads, Different Focus

Each attention head learns to specialize in a different type of relationship.

**Example specialization**

* **Head 1** → syntactic structure
* **Head 2** → semantic similarity
* **Head 3** → long-range connections
* **Head 4** → coreference resolution

When the outputs of all heads are combined, the model forms a **richer and more expressive representation**, integrating multiple linguistic perspectives at once.

👉 Multi-Head Attention allows the model to understand language **from several angles simultaneously**, rather than through a single relational lens.

---

## Masked Self-Attention (also called Causal Self-Attention)

Masked Self-Attention is a modified version of standard Self-Attention that **prevents the model from looking at future tokens** during processing.  

It enforces a strict left-to-right (causal) information flow:  
each position can only attend to itself and all positions **before** it — never to any position after it.

**Why it is needed**  
In autoregressive language modeling (e.g. GPT-style models), the model must generate text one token at a time.  
When predicting the next token, it is **not allowed to cheat** by seeing what comes later in the sequence.  
Masked Self-Attention guarantees that the prediction for position *i* depends **only on tokens 1 through i**.

**How the masking works (mechanically)**  
During the attention score computation:

1. Compute the raw attention scores (Q × Kᵀ) as usual  
2. Apply a **mask** to the score matrix:  
   - All positions where the query can **not** attend (i.e. future positions j > i) are set to a **very large negative value** (e.g. -1e9 or -∞)  
   - This is usually done with a **triangular mask** (upper triangle including or excluding the diagonal depending on implementation)

3. After masking → apply softmax  
   → Future positions receive **softmax probability ≈ 0**  
   → Effectively, no information flows forward in time

**Where it is used**  
- **Decoder-only models** (GPT, LLaMA, PaLM, Grok, etc.) → **all** self-attention layers are masked  
- **Encoder–Decoder models** (original Transformer, T5, BART) → only in the **Decoder** self-attention layers  
  (The Encoder uses unmasked / bidirectional Self-Attention)

**Summary**  
Masked Self-Attention = Self-Attention + future-masking → enforces autoregressive / causal generation by making sure no token can “see” anything that comes after it.

---

## Core Concept
Inside the Transformer, there is one single concept called **Attention**,
and depending on the situation and location, several different forms of Attention are used at the same time.
<br>

> **In other words, they are not separate/different techniques → they are simply variations of the same Attention mechanism.**
<br>

| Term                  | Meaning                                                                | Where it is used                                |
| --------------------- | ---------------------------------------------------------------------- | ----------------------------------------------- |
| Self-Attention        | Computes relationships between tokens within the same sequence         | "Encoder, Decoder"                              |
| Masked Self-Attention | Self-Attention that prevents attending to future tokens                | Decoder / GPT                                   |
| Cross-Attention       | Attends to a different sequence (usually the Encoder output)           | Decoder in Encoder–Decoder architectures        |
| Multi-Head Attention  | Performs multiple attention operations in parallel                     | All Transformer models                          |
| FlashAttention        | An algorithm that significantly speeds up attention computation        | Implementation / performance optimization level |

