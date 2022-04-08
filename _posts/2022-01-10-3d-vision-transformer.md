---
title: "3D Vision Transformer: Challenges" 
layout: post
post-image: "https://cdn.pixabay.com/photo/2016/09/23/20/54/doors-1690423_1280.jpg"
description: Challenges for designing 3D Vision Transformers
tags:
- vision transformer
- 3d
- segmentation
---

Recently I am working on designing 3D Vision Transformers for medical images. I have found many problems in my experiments, and i will list some of them in this blog.

### Challenges 1: Computational Cost

Computational cost can be mainly devided into three parts: number of parameters, FLOPs, and memory cost. More parameters will lead to a larger model size, while higher FLOPs means more float point calculation is needed to get the model output. Most previous lightweight works focus on these two metrics and the video memory cost is often neglected. However, video memory cost is actually also a very important issue. It determines what kind of devices are capable of training and testing a certain model. Many models these days need high-end GPUs with up to 24~48G video memory to train. And if you want to use a larger batch size, you have to get several GPUs to use "torch.nn.DataParallel". These three metrics are not always correlated, since a model with fewer parameter may still costs more graphical memory. 

High computational cost is the primary challenge for 3D vision transformers (actually it is also a big challenge for 2D vision transformers and that's why many people are working on lightweight transformers). We have to find less costly operations to subsititue SA to lower the cost and menawhile design efficient structures to reduce the memory cost.

### Challenges 2: Pure Transformer / CNN + Transformer?

Pure Transformer has been proved to have a similar modeling ability as CNNs. This causes pure transformer structures becoming more and more popular nowdays. However, such modeling ability only comes when the training samples are sufficient. Compared with pure transformer, CNNs have better structure prior, making them able to work without large-scale pretraining. Therefore, to build an EFFICIENT Vision Transformer, CNN+Transformer may be a better option. CNN can be used in the shallow layers to process the large-sized features (since the number of convolution parameters has nothing to do with the feature size) and transformer can be used to process the smaller features in deeper layers. Therefore, fine-grained local details can be efficient exploited by convolution, while the global-wise modeling only needed to be conducted on features after pooling. However, with sufficient computational resources and large scale datasets, many works show that pure transformers can achieve a better performance with some modifications, such as Swin-Transformer. More and more works show that maybe we should jump out of the canonical form of network structures and find new backbones. 

### Challenge 3: Modifications for Visual Tasks

This is also a critic issue for 2D vision transformers. Transformers are firstly proposed for NLP tasks, so directly applying it to visual tasks may cause incompatibility. For example, patches are widely used in vision transformers to convert an image into sequence. But is it really necessary? Will it harm the model performance especially for dense prediction tasks?

Personlly I believe vision transformers should abandon patches (or in other words, use 1*1 patches) to further improve the segmentation performance. The use of patches in vision transformers is more like a way to reduce the number of tokens, trying to embedding the local information of a small region into one single token. Such operation lacks flexibility and may cause negative influence.
