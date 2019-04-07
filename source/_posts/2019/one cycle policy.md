---
title: The 1cycle policy 
date: 2019-03-14 17:27:54
tags: [ML, Learning Rate]
categories: [ML]
---
[原文](https://sgugger.github.io/the-1cycle-policy.html)
本文介绍一种能够快速获得模型结果，并提高精度。首先介绍Leslie Smith在超参数（hyper-parameters ）方面的工作。他将其称为1cycle policy，能够快速地训练复杂模型。
# 大学习率
![](https://sgugger.github.io/images/art5_losses.png)  
0-41个epoch中，learning rate从0.08线性增长到0.8，然后在42-82个epoch内从0.8回落到0.08.  
high learning rate可以起到regularization的效果防止过拟合。这相当于防止跨入一个小的局部最优解。这表明SGD在寻找一个宽阔平坦的区域  

# Supplements
[1cycle policy](https://arxiv.org/abs/1803.09820)  
[Super-Convergence](https://arxiv.org/abs/1708.07120)
[Deep Residual Learning for Image Recognition](https://arxiv.org/abs/1512.03385)

