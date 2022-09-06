---
title: Decomposition For Light-Weight Vision Models
layout: post
post-image: "/assets/images/holisticDecomposition.png"
description: A brief review on image decomposition methods.
tags:
- light-weight
- decomposition
- interpolation
---

Decomposition, as the most intuitive method for reducing memory cost, has been forgotten by people since they usually leads to performance degration as well. This article reviews decomposition in deep learning and also introduces some great work in this field.

In deep learning era, GPU plays an very important role in model training for their great parallel computing abiliy. However, for high resolution real-world images, graphical memory usually tends to be insufficient. Therefore, many people use interpolation to down-sample the image. In my view downsampling can be also viewed as a method of decomposition. 

Besides downsampling, patch-sampling is another method that people use to solve the memory lacking problem. Patch-sampling refers to such method that for every single image, firstly devide the image into several small regions with overlap, then perform segmentation on each small region separately. The final segmentation mask is the fusion of these devided smaller segmentation masks. As a typical devide-and-conquer method, patch-sampling segmentation greatly reduce the memory consumption but also lead to model training process lack of global context, which proves to be actually very important for a better model performance. You see, many works today such as Non-Local models and vision Transformers want to model long-ranged dependencies in the network, but patches as input naturally harms the global context information.

The above two ways of decomposition both cause information loss for the model. Is there a way of loss-less decomposition? Actually there is. In 2019 *Medical Image Analysis*, Holistic Decomposition is proposed. This method uses the inverse operation of Sub-Pixel and Sub-Pixel itself in pair to realize low-cost patch-free segmentation. 

<center>
    <img src="/assets/images/subpixel.png" width="500">
    <br>
    Fig.1 Sub-Pixel
</center>

Sub-Pixel is a classic upsampling method proposed in CVPR 2016. It is originally designed for super-resolution but can also be used as a common upsampling layer. Generally speaking, it will convert several channels' feature into a bigger, single channel image with certain arrangement of the pixels.

<center>
    <img src="/assets/images/holisticDecomposition.png" width="500">
    <br>
    Fig.2 Holistic Decomposition
</center>

Holistic Decomposition is just the opposite. It will convert a single-channel image into several small images to form a multi-channel image. These two modules can be directly inserted into the front and end of existing models to realize patch-free segmemntation. However, the cost is that the model's width will shrink comparatively.

Finally there are also other patch-free methods. Some uses downsampled image as input but involves Super-Resolution to compensate the information loss ([MICCAI2021 Wang et al.](https://link.springer.com/chapter/10.1007/978-3-030-87193-2_13)); Some uses point-cloud segmentation to dynamiclly convert the large input image into points and omit the points with less information (MICCAI2021 Point-Unet). These methods has their pros and cons and you are encouraged to read them if interested. Currently, patch-free segmentation is still a less-noticed field that worth further exploration.
