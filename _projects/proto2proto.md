---
layout: distill
title: Proto2Proto: Can you recognize the car, the way I do?
description: CVPR 2022
date: 2022-03-28

authors:
  - name: Monish Keswani
    url: "archmaester.github.io"
    affiliations:
      name: IIT-H
  - name: Sriranjani Ramakrishnan
    url: ""
    affiliations:
      name: IIT-H
  - name: Nishant Reddy
    url: ""
    affiliations:
      name: IIT-H
  - name: Vineeth N B
    url: "https://people.iith.ac.in/vineethnb/"
    affiliations:
      name: IIT-H
---

### Abstract
Prototypical methods have recently gained a lot of attention due to their intrinsic interpretable nature, which is obtained through the prototypes. With growing use cases of model reuse and distillation, there is a need to also study transfer of interpretability from one model to another. We present Proto2Proto, a novel method to transfer interpretability of one prototypical part network to another via knowledge distillation. Our approach aims to add interpretability to the "dark" knowledge transferred from the teacher to the shallower student model. We propose two novel losses: "Global Explanation" loss and "Patch-Prototype Correspondence" loss to facilitate such a transfer. Global Explanation loss forces the student prototypes to be close to teacher prototypes, and Patch-Prototype Correspondence loss enforces the local representations of the student to be similar to that of the teacher. Further, we propose three novel metrics to evaluate the student's proximity to the teacher as measures of interpretability transfer in our settings. We qualitatively and quantitatively demonstrate the effectiveness of our method on CUB-200-2011 and Stanford Cars datasets. Our experiments show that the proposed method indeed achieves interpretability transfer from teacher to student while simultaneously exhibiting competitive performance.
