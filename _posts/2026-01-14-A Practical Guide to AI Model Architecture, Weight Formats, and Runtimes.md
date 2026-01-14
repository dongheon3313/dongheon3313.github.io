---
title: "[Basics]A Practical Guide to AI Model Architecture, Weight Formats, and Runtimes"
categories: ai
tags: [Basic, AI]
layout: single
share: false
---

## 1. Core Concepts (The Big Picture)

### [1] Model Architecture
- The **computational structure / mathematical form** of a model
- Defines *what kind of data the model can handle* and *how it processes that data*

### [2] Weight Format
- The **way trained numerical parameters (weights)** are stored
- Determines portability, safety, and compatibility with runtimes

### [3] Runtime (Inference Engine)
- The **engine that actually executes the model**
- Runs the architecture + weights on real hardware (CPU / GPU / accelerator)

> Architecture defines *what to compute*  
> Weight format defines *how parameters are stored*  
> Runtime defines *how and where computation happens*

---

## 2. Major Model Architectures

### CNN (Convolutional Neural Network)
- Neural networks specialized for **image processing**
- Repeatedly detects **local features**
- Small filters (kernels) slide across the image and detect:
  - “There is a line here”
  - “There is a corner here”

**Excels at**
- Edge detection
- Object detection
- Image segmentation

---

### Transformer
- Processes the entire input at once using **Self-Attention**
- Core question:
  > “How strongly is this token related to other tokens?”

**Excels at**
- Text understanding and generation
- Semantic and structural reasoning
- Vision-Language tasks

---

### Diffusion Models
- Start from **random noise**
- Gradually denoise step-by-step to generate images

**Excels at**
- Image generation
- Style transfer
- High-quality visual synthesis

---

### Hybrid Models (e.g. CNN + Transformer)
- Combine strengths of multiple architectures
- Common in modern vision systems
  - CNN for local features
  - Transformer for global context

---

## 3. Weight Formats (How Trained Numbers Are Stored)

### PyTorch Family
- `.safetensors`
  - Modern standard
  - Safe (no arbitrary code execution)
  - Fast loading
- `.bin`
  - Legacy PyTorch format

> Hugging Face `transformers` typically expects these formats

---

### ONNX (Open Neural Network Exchange)
- A **framework-independent computational graph**
- Represents a *frozen snapshot* of a trained model
- **Inference-only**

**Key idea**
> ONNX is not “training code”  
> It is a portable, fixed execution graph

---

### GGUF
- A **CPU-friendly, heavily quantized format** for LLMs
- Designed for `llama.cpp`
- Optimized for local execution

**Purpose**
- Run large language models without GPUs

---

### TensorRT Engine
- NVIDIA GPU–specific compiled artifact
- Generated from ONNX or framework models

**Characteristics**
- Extremely fast
- Hardware-specific
- Not portable

---

## 4. Runtimes (Who Executes the Model, and How)

Runtimes are typically designed **for specific weight formats**, which is why
**weight format + runtime usually form a pair**.

---

### PyTorch Runtime
- Dynamic computation graph
- Easy debugging and experimentation

**Trade-offs**
- Flexible
- Relatively slow
- Not ideal for production deployment

---

### TensorFlow Runtime
- Supports both static and dynamic graphs
- Strong ecosystem for training and deployment
- Common in large-scale production and research

**Characteristics**
- Mature tooling
- Good cross-platform support
- Slightly heavier for pure inference compared to ONNX Runtime

---

### ONNX Runtime
- Static computation graph
- Very fast
- Cross-platform (Windows / Linux / macOS)

**Best suited for**
- Deployment
- Product-level applications
- CAD / BIM / industrial software

---

### OpenVINO
- Intel’s inference toolkit
- Optimized for:
  - Intel CPU
  - iGPU
  - VPU (low-power accelerators)

**Strengths**
- Excellent CPU performance
- Low power consumption
- Popular in industrial and edge environments

---

### llama.cpp
- Runtime dedicated to GGUF
- Highly optimized for CPU execution

**Use case**
- Local LLM tools
- Offline AI applications

---

### TensorRT
- NVIDIA GPU–only runtime
- Ultimate performance for inference

**Trade-offs**
- Complex setup
- Hardware lock-in
- Limited portability

---

## 5. Why Format and Runtime Are a “Combination”

Weight formats are **static containers**  
Runtimes are **active executors**

If a runtime cannot understand a format, execution is impossible.

That’s why:
- `.gguf` → `llama.cpp`
- `.onnx` → ONNX Runtime / TensorRT / OpenVINO
- `.pt` → PyTorch Runtime

---

## 6. How to Choose (The Practical Rule)

All real-world decisions come from the intersection of:
- Your Data Type(Image, Text..)
- Your Hardware(Server, Local..)
- Your Application Goal(Deployment, Training..)

### Common Data Types
- Image
- Text
- Time Series
- Graph
- Generative data

---

## 7. Key Takeaway

> **Model architecture is a given**  
> **Weight format and runtime are engineering decisions**
