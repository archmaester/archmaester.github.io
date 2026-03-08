---
layout: page
title: what i've been up to
permalink: /ai-summary/
description: An AI-generated summary of what I'm currently working on and learning.
---

<div class="ai-summary-container">

<p class="ai-summary-meta text-muted">Last updated: 2026-03-08</p>

## Focus Areas

### Deep Learning Fundamentals
Strong, math-grounded coverage of core neural network building blocks — convolutions (2D, depthwise, pointwise), batch normalization, dropout, activation functions, and gradient computation. Posts derive formulas from first principles (e.g., why dL/dW = g·xᵀ, why 3×3 kernels dominate).

### Edge AI & Model Deployment
Practical focus on making models run efficiently: INT8 quantization (PTQ, QAT), mixed-precision training with BF16, depthwise-separable convolutions for mobile, and parameter reduction techniques. Understanding the full pipeline from FP32 training to edge deployment.

### Numerical Precision & Hardware
Deep understanding of floating point representation, precision-range tradeoffs, tensor core computation, and why BF16 works despite limited mantissa bits.

### Linear Algebra for ML
PCA, ZCA whitening, eigenvalue decomposition, covariance matrices — with clear connections to how these tools serve ML pipelines.

### ML Theory
Bias-variance tradeoff, ensemble methods (bagging, boosting, GBM, XGBoost), regularization techniques.

---

## Depth Indicators

| Topic | Depth |
|-------|-------|
| Convolutions (2D, depthwise, pointwise) | Deep |
| Quantization & mixed precision | Deep |
| Gradient derivations / backprop | Deep |
| Batch normalization | Moderate |
| Ensemble methods (RF, GBM, XGBoost) | Moderate |
| PCA / whitening | Moderate |
| Dropout / regularization | Moderate |
| LLM tooling (Claude usage) | Surface |
| Transformers / attention | Not yet covered |
| Generative models | Not yet covered |

---

## Learning Trajectory

Progressing from foundational deep learning math → deployment/optimization concerns → knowledge systems. The arc suggests movement toward understanding the full ML lifecycle: theory → implementation → efficient deployment.

## Technical Style

- **Math-first**: derives formulas before explaining intuition
- **Comprehensive**: posts are study-guide length, not quick notes
- **Practical grounding**: connects theory to real constraints (hardware, memory, latency)
- **Sources content** from AI discussions (ChatGPT, Claude) and synthesizes into structured write-ups

</div>
