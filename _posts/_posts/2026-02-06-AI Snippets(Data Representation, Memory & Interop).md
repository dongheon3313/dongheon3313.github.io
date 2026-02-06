---
title: "[Snippets]AI Snippets: Data Representation, Memory & Interop"
categories: ai
tags: [Snippets, AI]
layout: single
share: false
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
