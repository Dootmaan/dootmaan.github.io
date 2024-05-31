---
title: "Parameter Compression Based on Probability Density Function: An Idea" 
layout: post
post-image: "/assets/images/pdf4compression.png"
description: Maybe we can turn to store the probability density function to realize model compression
tags:
- lightweight
- model compression
- probability density function
---

I was thinking about using the probability density function to describe a parameter matrix. I don't know if it is feasible but theoretically speaking it is at least "runnable".

### Why do we need model compression

There are many reasons to employ model compression in real life, such as saving disk space or accelerating the file transfer speed over the internet. There are many compressed formats that are widely used in the current world, such as .jpg, .png (for images), .mp3, and .aac (for audio). It is the same reason that we need another compressed format to store the model weights efficiently. 

### Existing Methods

Currently, there are three methods for model compression: pruning, quantization, low-rank factorization, and maybe knowledge distillation as well. While pruning removes the "useless" part of a model, knowledge distillation seeks a simpler alternative to the originally complex model. These two methods both try to reduce the model size by shrinking the model structure. On the other hand, quantization reduces the bits used for storing a parameter, and low-rank factorization tries to decompose a full float number matrix into the multiplication of several smaller matrices. These two methods try to reduce the precision of a matrix to realize compression.

### Parameter Compression Based on Probability Density Function

It seems like precision reduction may be a better method for model compression (just like the model image compression technique usually would not reduce the image resolution but only do minor changes to the color of each pixel). Since we are dealing with float point numbers, some tricks such as only storing the difference between neighboring numbers with fewer bits would be impossible. Therefore, I think maybe we can use a regression method to find a similar probability density function that fits the parameter matrix and then only store the necessary information for the probability density function. When we need to use the model, we can simply restore the weights from this function.
