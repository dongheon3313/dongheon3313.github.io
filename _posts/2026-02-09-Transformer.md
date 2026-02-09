---
title: "[Basics]Transformer"
categories: ai
tags: [Basic, AI]
layout: single
share: false
---

## Transformer Architectures: Encoder–Decoder vs Decoder-Only

A decoder-only model can appear similar to an encoder–decoder architecture 
because it is also capable of processing input and generating output, 
which can make them seem equivalent at a glance.

From the outside, this seems true. Both architectures use attention and produce contextual representations.
However, **the internal information flow is fundamentally different**, and this leads to **real performance differences depending on the task**.

---

## Encoder–Decoder Architecture

**Flow**

```
Input sentence → Encoder → Fixed semantic representation (memory)
                               ↓
                        Decoder attends to this memory while generating
```

### Key Characteristics

* The **encoder converts the entire input into a stable semantic memory**.
* The **decoder repeatedly attends to the same encoder memory at every generation step**.
* The original input meaning remains **explicit and preserved** throughout generation.

### Implications

* The model maintains a **stable understanding of the source sentence**.
* Each decoding step references the **same unchanged encoder memory**, which helps maintain **consistency**.
* Especially strong for tasks like:

  * Machine translation
  * Summarization
  * Any task where output must stay faithful to the source

👉 The decoder always has **direct access to the original meaning**.

---

## Decoder-Only Architecture

**Flow**

```
Input tokens + Generated tokens → Single evolving sequence
                                → Generation over continuously updated hidden states
```

### Key Characteristics

* Input and output are treated as **one continuous sequence**.
* The model generates based on a **constantly evolving hidden state**.
* There is **no fixed external memory** representing the input.

### Implications

* As generation progresses, the **distance from the original input grows**.
* Input information can gradually **fade or become diluted**.
* The model relies more on **its internal evolving representation** than a stable reference.

👉 The original input meaning is **not explicitly preserved** and may weaken over time.

---

## Structural Difference in Information Flow

| Aspect                | Encoder–Decoder                                             | Decoder-Only                        |
| --------------------- | ----------------------------------------------------------- | ----------------------------------- |
| Input representation  | Fixed encoder memory                                        | Part of evolving sequence           |
| Reference to input    | Constant and explicit                                       | Indirect and fading                 |
| Information stability | High                                                        | Can weaken over time                |
| Best suited for       | Translation, faithful transformation                        | Open-ended generation, continuation |
| Attention pattern     | Encoder: bidirectional, Decoder: cross-attention to encoder | Causal self-attention only          |

---

## Why This Leads to Performance Differences

* **Encoder–Decoder** preserves the original meaning → better alignment, faithfulness, and consistency.
* **Decoder-Only** is more flexible and scalable → excels at long-form generation, reasoning, and continuation tasks.
* The difference is **not about capability**, but about **how information is stored and accessed during generation**.

---

## Intuition

* **Encoder–Decoder** → “Read once, keep the meaning fixed, generate while constantly looking back.”
* **Decoder-Only** → “Keep writing while updating your understanding as you go.”

Both are powerful, but their **information flow design shapes their strengths**.
