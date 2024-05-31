---
title: "Rethinking Instance Representation Learning in Multiple Instance Learning" 
layout: post
post-image: "/assets/images/pathology_patches.png"
description: Bag-level classifiers are good teachers for instance-level classifiers.
tags:
- Pathology
- CAMELYON
- MIL
---

Recently I have been working on a new Multiple Instance Learning (MIL) framework that can couple the patch representation learning with the bag-level classification. The process has been pretty hard, since I also have to deal with other businesses at the same time. However, I am still very excited about this idea since it seems to have a very sound logic.

### Current Multiple Instance Learning Methods and Their Limitations.

For a very long time, MIL on Whole Slide Images (WSI) has been separated into two independent procedures, which are patch feature embedding and bag-level classification to be specific. These two processes should have been trained end-to-end but is just prohibitively expensive for current GPUs. Therefore, people usually use an ImageNet pre-trained ResNet50 (or ViT and whatever. ViT performs better in my experiments btw) to embed the patches of a WSI into fixed 1024-vectors, and then train a bag-level classifier on these embeddings at a low cost. Another reason that people tend to use a pre-trained model to directly generate embeddings is that in most cases, we do not have the patch-level labels. We do not know whether a patch contains a tumor area or not, and therefore using a pre-trained network to directly project an image to a 1024-vector seems to be the most convenient way.

However, such compromise to computational cost inevitably leads to imperfect patch embedding performance. More specifically, when applying ImageNet pre-trained weights on pathology images, it will inevitably bring about a domain shift. However, since we do not have the instance-level labels, we can only retrain the patch embedder with pseudo labels. Existing methods that focus on the embedder training usually use the attention score of each instance to generate their pseudo labels, but in our work, we propose to use the bag-level classifier to directly generate new embeddings.
 
### Coupling the Two Processes in Multiple Instance Learning

Of course we want an End-to-End trained MIL if we have enough GPU memory. But unfortunately, in real life GPU memory is usually restricted at 24GB and that is just impossible for training with these gigapixel WSIs.  

We are glad to find out that recently more and more people started to look into this problem. In CVPR2023 paper [1], the authors propose to apply information bottleneck on the tiled patches of a WSI and each time only select 10% of the patches for end-to-end training. Our concurrent work has a similar idea, but instead of realizing orthodox end-to-end training at the cost of input information lost, we turn to realize a nearly end-to-end training with full input information. A general explanation of our solution is that we view the entire MIL pipeline as an Expectation-Maximization process, with patch embedding as E step and bag classification as M step. We iteratively optimize these two processes by fixing the other process, and gradually couple them together to realize a nearly end-to-end training. 

### Further Experiments

Since the paper has just been submitted to MICCAI 2023, we cannot give a detailed description of our solution yet. However, we will still work on this idea and find a better training scheme for the M step. 



### Reference
[1] Li H, Zhu C, Zhang Y, et al. Task-specific Fine-tuning via Variational Information Bottleneck for Weakly-supervised Pathology Whole Slide Image Classification. arXiv preprint arXiv:2303.08446, 2023.

