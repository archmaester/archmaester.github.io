---
layout: distill
title: Recall@k Surrogate Loss with Large Batches and Similarity Mixup
description: arxiv
date: 2021-08-21

authors:
  - name: Yash Patel
    url: "https://yash0307.github.io/"
    affiliations:
      name: VRG, CTU
  - name: Giorgos Tolias
    url: "https://archmaester.github.io"
    affiliations:
      name: VRG, CTU
  - name: Jiri Matas
    url: "https://cmp.felk.cvut.cz/~matas/"
    affiliations:
      name: VRG, CTU

---

### Abstract
Direct optimization, by gradient descent, of an evaluation metric, is not possible when it is non-differentiable, which is the case for recall in retrieval. In this work, a differentiable surrogate loss for the recall is proposed. Using an implementation that sidesteps the hardware constraints of the GPU memory, the method trains with a very large batch size, which is essential for metrics computed on the entire retrieval database. It is assisted by an efficient mixup approach that operates on pairwise scalar similarities and virtually increases the batch size further. When used for deep metric learning, the proposed method achieves state-of-the-art results in several image retrieval benchmarks. For instance-level recognition, the method outperforms similar approaches that train using an approximation of average precision. The implementation will be made public.
