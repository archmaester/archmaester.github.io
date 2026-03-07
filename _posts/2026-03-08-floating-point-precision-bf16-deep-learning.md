---
layout: post
title: "Understanding Floating Point Precision, BF16, and Why Deep Learning Training Still Works"
date: 2026-03-08
description: An explanation of floating point spacing, BF16 precision limitations, and how mixed precision training enables modern deep learning despite low-precision formats.
tags: [deep-learning, floating-point, bf16, mixed-precision, training]
---

Deep learning training relies heavily on floating point arithmetic. At first glance, formats like **BF16 (bfloat16)** appear too imprecise to support learning because they have very limited mantissa bits. Yet modern GPUs and TPUs train massive neural networks successfully using BF16.

This article explains why that works, focusing on:

- Floating point spacing
- Precision vs range
- Why BF16 appears problematic
- How deep learning training avoids those problems

---

## 1. Floating Point Representation

A normalized floating point number is stored as:

$$x = (-1)^{\text{sign}} \times (1 + \text{fraction}) \times 2^{\text{exponent}}$$

Where:

- **sign** = sign bit
- **fraction** = mantissa fraction
- **exponent** = power of two scaling

For example, FP32 has the layout:

```
[ sign | exponent | mantissa ]
[  1   |    8     |    23     ]
```

Because normalized floats assume the first bit is always `1`, FP32 effectively has **24 bits of precision**.

---

## 2. Spacing Between Representable Numbers

Floating point numbers are **not evenly spaced**.
The gap between two adjacent numbers depends on the exponent.

The spacing formula is:

$$\text{spacing} = 2^{(e - m)}$$

Where:

- **e** = exponent
- **m** = number of mantissa bits

For FP32:

```
m = 23
spacing = 2^(e - 23)
```

### Example: Near 1

```
e = 0
spacing = 2^-23 ≈ 1.19e-7
```

Numbers near 1 look like:

```
1.0000000
1.0000001
1.0000002
```

### Example: Near 1e9

```
1e9 ≈ 2^30
spacing = 2^(30 - 23) = 2^7 = 128
```

Numbers near 1e9 look like:

```
1000000000
1000000128
1000000256
```

So as numbers grow larger, **precision decreases**.

Floating point maintains **constant relative precision**, not constant absolute precision.

---

## 3. FP32 vs FP16 vs BF16

| Format | Sign | Exponent | Mantissa |
|--------|------|----------|----------|
| FP32   | 1    | 8        | 23       |
| FP16   | 1    | 5        | 10       |
| BF16   | 1    | 8        | 7        |

Key observation:

```
BF16 keeps the same exponent as FP32
```

This means BF16 preserves the **same dynamic range**.

### Range comparison

| Format | Smallest | Largest  |
|--------|----------|----------|
| FP32   | ~1e-38   | ~3e38    |
| BF16   | ~1e-38   | ~3e38    |
| FP16   | ~6e-5    | ~65504   |

So BF16 can represent **very small gradients** and **very large activations**, unlike FP16.

---

## 4. The Precision Problem With BF16

BF16 only has **7 mantissa bits**.

Spacing near 1 becomes:

$$\text{spacing} = 2^{-7} = 0.0078125$$

Numbers near 1 look like:

```
1.0000
1.0078
1.0156
```

This appears catastrophic for training.

Example:

```
weight = 1
gradient = 1e-5
update = 1e-5
```

BF16 cannot represent differences that small near 1.

So how does training still work?

---

## 5. The Key Trick: Mixed Precision Training

Modern training **does not store weights in BF16**.

Instead:

```
Weights (master copy) → FP32
Weights (compute copy) → BF16
Activations → BF16
Gradients → BF16
Optimizer state → FP32
```

Training loop:

```
FP32 weights
      ↓
convert to BF16
      ↓
forward pass
      ↓
backward pass
      ↓
gradients
      ↓
convert to FP32
      ↓
optimizer update
      ↓
update FP32 weights
```

So **critical updates happen in FP32 precision**.

---

## 6. Tensor Core Computation

Modern GPUs use tensor cores which operate like this:

```
BF16 × BF16 → FP32 accumulate
```

During matrix multiplication:

$$C = A \times B$$

each output element is computed as:

$$c = \sum (a_i \times b_i)$$

Even if inputs are BF16, **the accumulation happens in FP32**.

Example:

```
1e-10
+1e-10
+1e-10
...
```

Thousands of such contributions accumulate into a meaningful value before being rounded.

---

## 7. Gradient Accumulation

Gradients are typically sums of many terms:

$$g = \sum (x_i \times \delta_i)$$

Example:

```
1e-10
1e-10
1e-10
...
```

If 10,000 terms accumulate:

```
10000 × 1e-10 = 1e-6
```

Now the gradient magnitude becomes large enough to survive BF16 rounding.

---

## 8. Optimizers Accumulate Updates

Optimizers like **Adam** or **momentum SGD** accumulate gradients over time.

Example:

$$m_t = \beta \, m_{t-1} + (1 - \beta) \, g_t$$

Even if each gradient is tiny, repeated accumulation increases magnitude until the update becomes meaningful.

---

## 9. What Precision Is Actually Lost

BF16 mostly removes **fine-grained precision**, not the overall magnitude.

Example:

True gradient:

```
1.234567e-4
```

BF16 stored:

```
1.234e-4
```

So a few digits vanish, but the **scale of the gradient remains correct**.

Deep learning optimization is robust to this noise.

---

## 10. Why BF16 Works So Well

Deep learning needs two properties:

```
large dynamic range
reasonable precision
```

BF16 provides:

```
same range as FP32
lower precision
```

The lost precision mostly behaves like **additional gradient noise**, which SGD already tolerates.

---

## 11. Final Intuition

BF16 training works because:

1. **Matrix multiplications accumulate in FP32**
2. **Gradients are sums of many contributions**
3. **Weights and optimizer states stay in FP32**
4. **SGD is robust to small rounding errors**

So even though BF16 spacing is large, the important numerical operations still maintain enough precision.

---

## Key Takeaway

BF16 does not work because it is precise.

It works because modern deep learning systems carefully design the training pipeline so that:

```
precision-sensitive steps use FP32
throughput-heavy steps use BF16
```

This provides the best combination of **speed, memory efficiency, and training stability**.
