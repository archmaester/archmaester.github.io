---
layout: post
title: "Quantization in CNNs: From FP32 Training to INT8 Deployment"
date: 2026-02-14
description: A practical walkthrough of how CNN weights go from 32-bit floating point to 8-bit integers — and why it barely hurts accuracy.
tags: [edge-ai, deep-learning, quantization]
---

## Why This Matters

Deep neural networks are typically trained in **FP32 precision**, but most real-world deployments --- especially on edge devices --- rely on **lower precision arithmetic like INT8**.

Why does this work? How are such networks trained? Can distillation help? And what is the actual numeric range of weights in practice?

This post walks through the full story --- from floating-point CNN training to INT8 weight deployment --- with practical intuition and engineering insight.


## 1. Why Quantization Exists

FP32 is expensive:

- 4 bytes per parameter
- Higher memory bandwidth
- More energy consumption
- Slower inference on CPUs and edge hardware

INT8 gives:

- **4× smaller models**
- **2--4× faster inference**
- Lower memory traffic
- Better hardware utilization

The surprising part?

> Most CNNs barely lose accuracy when weights are reduced to INT8.

To understand why, we need to examine how CNN weights behave.


## 2. What Is the Typical Range of CNN Weights?

After training, CNN weights are:

- Zero-centered
- Gaussian-like distributed
- Small in magnitude
- Heavily concentrated near zero

In practice:

- **~99% of weights lie in [-0.1, +0.1]**
- Rare outliers may approach ±1
- Values beyond ±2 are extremely unusual

Why so small?

1. **Kaiming initialization** starts small.
2. **Weight decay** shrinks norms.
3. **BatchNorm** absorbs scale.
4. Networks rely on *relative* patterns, not large magnitudes.

This small range is the reason INT8 works.


## 3. What INT8 Actually Represents

Signed INT8 range: **-128 to +127** (256 discrete levels).

To map floating-point weights to this range, we use **linear quantization**:

    q = round(w / scale) + zero_point

Where:
- `w` is the original FP32 weight
- `scale` determines the step size between quantized values
- `zero_point` shifts the mapping so that FP32 zero maps exactly to an integer

For **symmetric quantization** (zero_point = 0), mapping weights in [-0.1, +0.1] to [-128, +127]:

    scale = 0.2 / 255 ≈ 0.000784

Each INT8 step represents less than 0.001 in the original FP32 space. That is far finer than what the network needs to preserve its learned representations.


## 4. Post-Training Quantization (PTQ)

The simplest approach: train in FP32, quantize afterwards.

**Steps:**

1. Train a model normally in FP32
2. Collect calibration statistics (min/max of weights and activations)
3. Compute scale and zero_point per layer (or per channel)
4. Round all weights to INT8

**Per-channel vs. per-tensor:**

- **Per-tensor**: one scale for the entire weight tensor. Simple but coarse.
- **Per-channel**: one scale per output channel. More accurate, widely supported on modern hardware.

**When PTQ works well:**
- Large models (ResNet-50, EfficientNet)
- Weights are well-distributed with few outliers
- Activations have stable ranges

**When PTQ struggles:**
- Depthwise convolutions (small accumulation, sensitive to rounding)
- Lightweight models where every bit of precision counts
- Layers with high dynamic range or outlier activations


## 5. Quantization-Aware Training (QAT)

When PTQ isn't enough, we simulate quantization *during training*.

**How it works:**

1. Insert **fake quantization nodes** into the training graph
2. Forward pass: weights and activations are quantized then dequantized (simulating rounding error)
3. Backward pass: use the **Straight-Through Estimator (STE)** --- gradients pass through the rounding operation as if it were the identity function
4. The network learns to be robust to quantization noise

**Why QAT helps:**

The model adapts its weight distribution during training. It learns to:
- Avoid outlier weights that waste quantization range
- Spread information across bins more uniformly
- Compensate for rounding errors through adjacent layers

QAT typically recovers **most or all** of the accuracy lost during PTQ, even for sensitive architectures like MobileNet.


## 6. Can Knowledge Distillation Help?

**Yes** --- distillation and quantization are complementary.

The idea: train a quantized **student** model using soft targets from a full-precision **teacher**.

    Loss = α × CrossEntropy(student, hard_labels) + (1 - α) × KL(student_logits, teacher_logits)

Why this helps quantized models:

- **Soft targets carry richer information** than hard labels (inter-class relationships)
- The student learns smoother decision boundaries that are more robust to quantization noise
- Distillation can compensate for capacity lost during quantization

**Practical recipe:**

1. Train a full-precision teacher
2. Initialize the student with the same architecture
3. Train with QAT + distillation loss
4. Quantize the student to INT8

This combination --- QAT + distillation --- is the gold standard for deploying lightweight quantized models on edge hardware.


## 7. The Full Pipeline

Putting it all together, here is a typical production pipeline:

**Stage 1: FP32 Training**
- Standard training with Kaiming init, BatchNorm, weight decay
- Result: a well-trained FP32 model

**Stage 2: Calibration**
- Run a representative dataset through the model
- Record activation ranges per layer
- Decide quantization scheme (symmetric vs. asymmetric, per-tensor vs. per-channel)

**Stage 3: PTQ Attempt**
- Quantize weights and activations to INT8
- Evaluate accuracy drop
- If acceptable (< 1% drop), deploy

**Stage 4: QAT (if needed)**
- Fine-tune with fake quantization nodes for 10--20% of original training epochs
- Optionally add distillation from the FP32 teacher
- Re-evaluate accuracy

**Stage 5: Export and Deploy**
- Export quantized model to target runtime (TFLite, ONNX Runtime, TensorRT)
- Validate on-device accuracy and latency
- Ship it


## 8. Common Pitfalls

A few things that catch practitioners off guard:

- **Ignoring activation quantization.** Weights are easy; activation ranges vary per input and need calibration.
- **Using per-tensor quantization on depthwise layers.** Per-channel is almost always better here.
- **Skipping BatchNorm folding.** BN layers should be folded into conv weights before quantization to avoid unnecessary operations.
- **Calibrating on unrepresentative data.** Your calibration set must reflect real deployment conditions.
- **Expecting miracles from 4-bit.** INT8 is mature; INT4 and lower still require careful architecture-specific tuning.


## Final Takeaway

Quantization works because CNN weights are naturally well-behaved: small, centered, and smooth. INT8 provides more than enough resolution to represent them faithfully.

The practical recipe is straightforward:

1. **Try PTQ first** --- it's free and often sufficient
2. **Use QAT** when PTQ drops accuracy
3. **Add distillation** for the hardest cases

Together, these techniques make it possible to deploy powerful CNNs on devices with a fraction of the compute and memory budget --- without sacrificing the accuracy that makes them useful.
