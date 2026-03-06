---
layout: post
title: "2D Convolution in Image Processing — Complete Summary"
date: 2026-03-06
description: A complete guide to 2D convolution covering kernels, padding, stride, output size, and why modern CNNs prefer 3x3 filters.
tags: [deep-learning, convolutions, image-processing, cnn]
---

## 1. What is 2D Convolution?

Images are represented as **2D matrices of pixel values**.

Example grayscale image:

```
10 20 30
40 50 60
70 80 90
```

A **convolution** operation applies a small matrix called a **kernel (filter)** to the image to produce a new image.

Common uses:

- Edge detection
- Blurring
- Sharpening
- Feature extraction in CNNs

The kernel **slides across the image**, computing a weighted sum of the pixels it covers.

---

## 2. Kernel (Filter)

A kernel is a small matrix such as:

```
1  0 -1
1  0 -1
1  0 -1
```

Typical kernel sizes:

| Kernel | Usage |
|------|------|
| 1×1 | channel mixing |
| 3×3 | most common |
| 5×5 | larger context |
| 7×7 | early CNN layers |

---

## 3. How Convolution Works (Step-by-Step)

For each kernel position:

1. Place kernel on image patch
2. Multiply corresponding elements
3. Sum the results
4. Output becomes one pixel

Example:

Image patch

```
1 2 3
4 5 6
7 8 9
```

Kernel

```
1 0 -1
1 0 -1
1 0 -1
```

Multiply elementwise and sum:

```
(1×1) + (2×0) + (3×-1)
+ (4×1) + (5×0) + (6×-1)
+ (7×1) + (8×0) + (9×-1)
```

Result → output pixel.

---

## 4. Mathematical Definition

For 2D convolution:

```
y(x,y) = Σ Σ  I(i,j) * K(x-i, y-j)
```

Where:

- `I` = input image
- `K` = kernel
- `y` = output image

Important part:

```
K(x-i, y-j)
```

This causes **kernel flipping**.

---

## 5. Kernel Flipping

True mathematical convolution **flips the kernel** before sliding.

Example kernel:

```
1 2 3
4 5 6
7 8 9
```

Flip horizontally:

```
3 2 1
6 5 4
9 8 7
```

Flip vertically:

```
9 8 7
6 5 4
3 2 1
```

This flipped kernel is used during convolution.

### Why flipping exists

Because convolution is defined using:

```
h[n - k]
```

This reverses the filter in signal processing.

---

## 6. Convolution vs Cross-Correlation

Most deep learning libraries **do not flip the kernel**.

They compute:

```
y(x,y) = Σ Σ I(x+i, y+j) * K(i,j)
```

This is called **cross-correlation**.

In CNNs the difference doesn't matter because **kernel weights are learned during training**.

---

## 7. Output Size Formula

The size of the output after convolution is:

```
O = (N - K + 2P)/S + 1
```

Where:

| Symbol | Meaning |
|------|------|
| N | input size |
| K | kernel size |
| P | padding |
| S | stride |
| O | output size |

This formula applies to **height and width separately**.

---

## 8. Understanding the +1

The +1 exists because we count **positions**, not distances.

Kernel start positions range from:

```
0 → (N + 2P - K)
```

Number of positions:

```
last - first + 1
```

Therefore:

```
O = (N + 2P - K)/S + 1
```

Another intuition:

```
Output = first placement + number of slides
```

---

## 9. Padding

Padding adds extra pixels around the border of the image.

Most common padding:

### Zero padding

Example:

Original

```
1 2 3
4 5 6
7 8 9
```

After padding (P=1)

```
0 0 0 0 0
0 1 2 3 0
0 4 5 6 0
0 7 8 9 0
0 0 0 0 0
```

---

## 10. Padding Needed for "Same Output Size"

If we want:

```
Output size = Input size
```

Then padding must satisfy:

```
P = (K - 1)/2
```

Examples:

| Kernel | Padding |
|------|------|
| 3×3 | 1 |
| 5×5 | 2 |
| 7×7 | 3 |

---

## 11. Why Even Kernels Are Rare

Example:

```
K = 4
P = (4-1)/2 = 1.5
```

Padding must be integer.

Thus symmetric padding is impossible.

Even kernels also lack a **center pixel**, which complicates alignment.

Therefore CNNs usually use **odd kernels (3,5,7)**.

---

## 12. Stride

Stride determines how far the kernel moves each step.

### Stride = 1

Kernel moves 1 pixel each time.

Pros:

- maximum spatial detail

Cons:

- high computation

---

### Stride = 2

Kernel skips pixels.

Example:

```
Input 32×32 → Output 16×16
```

Pros:

- downsampling
- lower computation

Cons:

- information loss

Large strides (>2) are rarely used.

---

## 13. Kernel Size Tradeoffs

| Kernel | Parameters | Effect |
|------|------|------|
| 3×3 | 9 | small local features |
| 5×5 | 25 | larger context |
| 7×7 | 49 | very large context |

Compute grows **quadratically** with kernel size.

---

## 14. Why Modern CNNs Prefer 3×3 Kernels

Instead of one 5×5 kernel:

```
parameters = 25
```

Two 3×3 layers:

```
parameters = 9 + 9 = 18
```

Advantages:

- fewer parameters
- more nonlinearities
- deeper networks

This idea was popularized in **VGG and ResNet**.

---

## 15. Parameter Count in CNNs

Number of parameters in a convolution layer:

```
Parameters = K × K × Cin × Cout
```

Example:

```
K = 3
Cin = 64
Cout = 128
```

```
3×3×64×128 = 73,728 parameters
```

Kernel size strongly affects computational cost.

---

## 16. 1×1 Convolution

A special convolution used in modern CNNs.

Kernel:

```
1×1
```

Purpose:

- mix channel information
- reduce dimensionality
- computationally cheap

Used heavily in **ResNet, MobileNet, GoogLeNet**.

---

## 17. Typical CNN Layer Configuration

Most modern architectures use:

```
Kernel = 3×3
Stride = 1
Padding = 1
```

This preserves spatial resolution.

Downsampling layers typically use:

```
Kernel = 3×3
Stride = 2
Padding = 1
```

---

## 18. Key Intuition

Convolution can be understood as:

"A small detector scanning the image to find specific patterns."

Each kernel detects patterns such as:

- edges
- corners
- textures
- object parts

The output map shows **where those patterns occur**.

---

## 19. Important Design Rules

Good CNN design usually follows:

```
1. Use odd kernels (3×3)
2. Use stride = 1 for feature extraction
3. Use stride = 2 for downsampling
4. Use padding = (K-1)/2 to preserve size
```

---

## Final One-Line Summary

**2D convolution slides a small kernel across an image, computing weighted sums of local pixel neighborhoods to detect patterns and extract features, with output size controlled by kernel size, stride, and padding.**
