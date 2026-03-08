---
layout: concept
title: "Dropout"
date: 2026-03-08
description: "Randomly dropping out neurons during training to prevent co-adaptation of features."
tags: [deep-learning, regularization, neural-networks]
paper: "Dropout: A Simple Way to Prevent Neural Networks from Overfitting (Srivastava et al., 2014)"
---

## Core Idea
Randomly "dropping out" (setting to zero) a subset of neurons during training to prevent complex co-adaptation of features.

## Motivation
* **Co-adaptation:** Neurons become overly dependent on specific other neurons to correct errors.
* **Ensemble Approximation:** Efficiently approximates training $2^n$ different "thinned" architectures and averaging them at test time.

## Formalism (Training)
For a layer $l$ with activations $y^{(l)}$:
1.  **Bernoulli Mask:** $r^{(l)} \sim \text{Bernoulli}(p)$
2.  **Gated Input:** $\tilde{y}^{(l)} = r^{(l)} * y^{(l)}$
3.  **Forward Pass:** $z_i^{(l+1)} = w_i^{(l+1)}\tilde{y}^{(l)} + b_i^{(l+1)}$
4.  **Inference Scaling:** Multiply weights by $p$ (Standard) or divide activations by $1-p$ during training (**Inverted Dropout**).

## Pros & Cons
| Pros | Cons |
| :--- | :--- |
| Significant reduction in overfitting. | Increases total training time (2x-3x). |
| Computationally cheap ($O(n)$ masking). | Adds hyperparameter $p$ (keep probability). |
| Works across MLP, CNN, and RNN layers. | Can interfere with Batch Normalization statistics. |

## Implementation Snippet (PyTorch)
```python
# During Training: model.train()
# During Inference: model.eval()
dropout = torch.nn.Dropout(p=0.5)
output = dropout(layer_activations)
```
