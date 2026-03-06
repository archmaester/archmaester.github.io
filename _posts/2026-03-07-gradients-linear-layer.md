---
layout: post
title: "Understanding Gradients in a Linear Layer: Why dL/dW = g xᵀ"
date: 2026-03-07
description: A step-by-step derivation of the weight gradient in a linear layer, explaining why the 3D Jacobian tensor collapses into a simple outer product during backpropagation.
tags: [deep-learning, backpropagation, linear-algebra, neural-networks]
---

When studying neural network backpropagation, one of the most common operations is the gradient of a **linear layer**:

$$y = Wx$$

Many people find the derivation confusing because the derivative of a vector with respect to a matrix technically produces a **3D tensor**, yet in practice we end up with a simple **outer product**.

This article explains the full derivation step-by-step and clarifies why the tensor disappears.

---

## 1. Forward Pass

Consider the linear transformation:

$$y = Wx$$

### Shapes

```
W ∈ ℝ^(m × n)
x ∈ ℝ^n
y ∈ ℝ^m
```

Element-wise form:

$$y_i = \sum_{j=1}^{n} W_{ij} x_j$$

Each output is a weighted sum of the inputs.

---

## 2. Derivative of y with Respect to W

We compute the derivative of each output element with respect to each weight.

$$\frac{\partial y_i}{\partial W_{kl}}$$

Substitute the definition of $y_i$:

$$y_i = \sum_j W_{ij} x_j$$

Taking the derivative:

$$\frac{\partial y_i}{\partial W_{kl}}
=
\frac{\partial}{\partial W_{kl}}
\left(\sum_j W_{ij} x_j\right)$$

Only the term where $i=k$ and $j=l$ survives.

$$\frac{\partial y_i}{\partial W_{kl}} = \delta_{ik} x_l$$

where

$$\delta_{ik} =
\begin{cases}
1 & i=k \\
0 & i\ne k
\end{cases}$$

This is the **Kronecker delta**.

---

## 3. The True Jacobian

Notice the indices:

$$\frac{\partial y_i}{\partial W_{kl}}$$

So the derivative depends on:

- output index $i$
- row index of $W$ → $k$
- column index of $W$ → $l$

Therefore

$$\frac{\partial y}{\partial W}$$

has three indices:

```
(i, k, l)
```

Shape:

```
m × m × n
```

So the **true Jacobian is a 3-D tensor**.

---

## 4. Why Deep Learning Doesn't Use This Tensor

In neural networks we usually have a scalar loss:

$$L(y)$$

What we actually need is:

$$\frac{\partial L}{\partial W}$$

not

$$\frac{\partial y}{\partial W}$$

---

## 5. Apply the Chain Rule

Using the chain rule:

$$\frac{\partial L}{\partial W_{kl}}
=
\sum_{i=1}^{m}
\frac{\partial L}{\partial y_i}
\frac{\partial y_i}{\partial W_{kl}}$$

Substitute the earlier result:

$$\frac{\partial y_i}{\partial W_{kl}} = \delta_{ik} x_l$$

So

$$\frac{\partial L}{\partial W_{kl}}
=
\sum_i
\frac{\partial L}{\partial y_i}
\delta_{ik} x_l$$

---

## 6. Kronecker Delta Collapses the Sum

Because the delta is nonzero only when $i=k$:

$$\frac{\partial L}{\partial W_{kl}}
=
\frac{\partial L}{\partial y_k} x_l$$

---

## 7. Matrix Form

Define the **output gradient**

$$g = \frac{\partial L}{\partial y}$$

where

```
g ∈ ℝ^m
```

Then

$$\frac{\partial L}{\partial W_{kl}} = g_k x_l$$

This is exactly the definition of an **outer product**:

$$(g x^T)_{kl} = g_k x_l$$

So the final result is

$$\boxed{\frac{\partial L}{\partial W} = g x^T}$$

---

## 8. Shape Check

```
g ∈ ℝ^m
xᵀ ∈ ℝ^(1 × n)
```

Outer product:

```
m × n
```

This matches the shape of $W$.

---

## 9. Intuition

Each weight connects:

```
input j → output i
```

The gradient for that weight equals:

```
(output gradient at i) × (input value at j)
```

So every weight update is simply:

```
gradient = output_gradient × input
```

---

## 10. Final Mental Model

For a linear layer

$$y = Wx$$

backpropagation always produces:

$$\boxed{\text{weight gradient} = (\text{output gradient}) \otimes (\text{input})}$$

or

$$\frac{\partial L}{\partial W} = g x^T$$

---

## 11. Python Implementation

In NumPy or PyTorch:

```python
y = W @ x

g = dL_dy

dL_dW = g[:, None] @ x[None, :]
```

Here:

```
@ = matrix multiplication
```

The result is the outer product producing the weight gradient.

---

## Key Takeaway

Even though the true derivative

```
dy/dW
```

is a **3D tensor**, backpropagation immediately contracts it with

```
dL/dy
```

which simplifies the result to the much cleaner

```
dL/dW = g xᵀ
```

This elegant structure is why linear layers are computationally efficient and map well to GPUs.
