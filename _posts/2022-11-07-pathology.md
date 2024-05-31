---
title: "Pathology Data and How to Handle Them" 
layout: post
post-image: "/assets/images/pathology.png"
description: I have so many questions about pathology data.
tags:
- Pathology
- HCC
- MIL
---

I have to admit that pathology data are really hard to deal with.

### Whole Slide Images

A pathology dataset contains many Whole Slide Images (WSI). WSIs are designed to be like microscope images. Therefore, it also includes images of different magnifications. Level 0 has the largest spatial dimension of all the levels, usually up to ~60000 x 60000. How to properly deal with such huge images is the key problem of pathology data classification. 

Usually, people choose to use tiling to split a WSI into several small non-overlap regions. However, dividing the entire image into several small parts does not mean each part still can use the original label of the WSI. For example, for an ES rating III WSI, some of its patches may not contain tumors at all and should be rated as I. However the problem is that we do not have these patch-level labels. To mitigate this problem, multi-instance learning is usually used.

### Multi-Instance Learning

Multi-instance Learning refers to the method that:
1. we build up a bag using many instances. e.g. we can use the image patches to form a bag.
2. label the bag. If there is one positive instance in a bag, then the bag is positive. If all instances are negative in a bag, then the bag has a negative label.
3. training on these bag-level samples. 

Technically the bag can consist of patches from all kinds of WSIs, but in practice, usually a bag is just a combination of all its patches (i.e. a bag=a WSI). And what if there are too many patches in a WSI? To solve this issue, we will usually use an ImageNet pre-trained ResNet50 to embed each patch into a 1024-vector. Therefore, a gigapixel WSI can be converted into N 1024-vectors, and each 1024-vector is an instance of the bag. The final bag-level representation can be obtained by pooling or attention mechanism.

MIL is absolutely dominant right now in histopathology analysis, since we usually only have the slide-level labels instead of the fine-grained patch-level's. MIL aims to convert a conventional classification problem into finding if there are any positive instances in a bag, which to some extent is reasonable indeed.

### Some of My Attempts

It is quite time-consuming to preprocess a WSI into several patches, let alone we have to convert these patches into 1024-dimension features later. I want to know if there exists any method that can quickly process a given WSI. (My task is HCC early recurrence)

First, I tried the most naive method, which is giving each patch the label of its parent WSI. I then train a model based on these patches. For a given test WSI, all of its patches are sent into this model, and we use the average prediction of these patches as the final output for the WSI. This method performs really badly with a ~58% Acc.

Then I randomly crop patches from a WSI to form a patch sequence (or a very small bag for MIL). I designed different kinds of models to directly predict the results, but all of them cannot surpass 63% Acc, which is still not ideal. After all, I was forcing patch-level models to fit the slide-level labels, and this inevitably resulted in errors.

### My Further Plan

After some discussion with my colleagues, I decided to walk back to the trail of MIL. There have also emerged many new MIL methods nowadays. For example, DTFD-MIL proposes to randomly split a bag into several pseudo-bags. Of course this can also lead to a mislabeling problem, since a pseudo-bag from a positive parent bag may contain no positive instance at all. The authors propose to solve this problem by distillation. In other words, the features of these pseudo-bags will be fused together (or simply selected) to form the final representation of the parent bag. The use of pseudo-bags increases the sample count, and boosts the model performance according to that paper.

Another work is Scaling Vision Transformers to Gigapixel Images. This work uses a hierarchy to imitate pathologists' way of working. It gradually aggregates fine-grained patches into coarse-grained large-scale patches. 

For me, right now I am just doing the preprocessing, such as tiling the WSI and encoding each patch into a 1024-dimension vector with the pre-trained ResNet50. Since each WSI is finally encoded into an Nx1024 bag, I can then build a classification network based on these samples with moderate GPU memory consumption.  