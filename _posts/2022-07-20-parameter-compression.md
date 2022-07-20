---
title: "Parameter Compression Based on Probability Density Function: An Idea" 
layout: post
post-image: "/assets/images/nonlocal_pooling.png"
description: Maybe we can turns to store the probability density function to realize model compression
tags:
- lightweight
- model compression
- probability density function
---

I was thinking about using probablity density function to describe a parameter matrix. I dont know if it is feasible but theoretically speaking it is at least "runnable".

### Why we need model compression

There are many reasons to employ model compression in real-life, such as saving disk space or accelerating the file transfer speed over internet. There are many compressed format are widely used in current world, such as .jpg, .png (for images), .mp3, .aac (for audios). It is the same reason that we need another compressed format to store the model weights efficiently. 

### Existing Methods

Currently, there are three methods for model compression: pruning, quantization, low-rank factorization, and maybe knowledge distillation as well. While pruning remove the "useless" part of a model, knowledge distillation seeks a simpler alternative of the originally complex model. These two methods both try to reduce the model size by shrinking the model structure. On the other hand, quantization reduce the bits used for storing a paramater, and low-rank factorization tries to decompose a full float number matrix into the mulplication of several smaller matrices. These two methods try to reduce the precision of a matrix to realize compression.

### Parameter Compression Based on Probability Density Function

It seems like precision reduction may be a better method for model compression (just like model image compression technique usually would not reduce the image resolution but only do minor changes to the color of each pixel). Since we are dealing float point numbers, some tricks such as only storing the difference between neighboring numbers with fewer bits would be impossible. Therefore, i think maybe we can use a regression method to find a similar probability density function that fits the parameter matrix and than only store the necessary information for the probability density function. When we need to use the model, we can simply restore the weights from this function.
