---
layout: concept
title: "Dropout"
date: 2026-03-08
tags: [deep-learning, regularization, neural-networks]
paper:
  title: "Dropout: A Simple Way to Prevent Neural Networks from Overfitting"
  authors: "Srivastava, Hinton, et al."
  year: 2014
  url: "https://jmlr.org/papers/v15/srivastava14a.html"
---

- **Motivation:** Prevents "co-adaptation" (feature dependency); approximates an ensemble of $2^n$ thinned networks.
- **Equation:** $r^{(l)} \sim \text{Bernoulli}(p)$; $\tilde{y}^{(l)} = r^{(l)} * y^{(l)}$.
- **Training (Inverted):** $a_{train} = \frac{a \cdot mask}{1-p}$ (keeps expected sum consistent).
- **Inference:** $a_{test} = a$ (all neurons active; no scaling needed).
- **Pros:** Robust generalization; $O(n)$ overhead; prevents overfitting in deep/wide nets.
- **Cons:** Increases training time (approx. 2x); requires tuning $p$ (dropout rate).
- **Impl:** `nn.Dropout(p)` (PyTorch) or `layers.Dropout(p)` (Keras).
