---
title: HAR
date: 2019-05-11 09:15:13
tags: ［论文］
categories: [ML]
---

# 数据集
[HAR](https://archive.ics.uci.edu/ml/datasets/human+activity+recognition+using+smartphones)  
该数据集包含561个特征,拥有6个分类.共10299条数据. 利用智能手机,通过加速度计和陀螺仪,以50HZ的频率采样得到3维的加速度和角速度信息.

# LogisticRegression
![](/blog_images/2019-05-11-09-35-06.png)![对附近10个数据做平均](/blog_images/2019-05-11-09-53-54.png)

# SVM
相关论文使用SVM做分类器.
![](/blog_images/2019-05-11-09-47-10.png)

# 单隐层
结构

    Sequential(
    (0): Linear(in_features=561, out_features=600, bias=True)
    (1): Sigmoid()
    (2): Linear(in_features=600, out_features=6, bias=True)
    )

![](/blog_images/2019-05-11-10-09-57.png)![](/blog_images/2019-05-11-10-09-50.png)


结构

    Sequential(
    (0): Linear(in_features=561, out_features=600, bias=True)
    (1): Tanh()
    (2): Linear(in_features=600, out_features=6, bias=True)
    )

![](/blog_images/2019-05-11-10-11-55.png)![loss](/blog_images/2019-05-11-11-16-21.png) 

结构

    Sequential(
    (0): Linear(in_features=561, out_features=600, bias=True)
    (1): ReLU()
    (2): Linear(in_features=600, out_features=6, bias=True)
    )

![](/blog_images/2019-05-11-10-18-10.png)![](/blog_images/2019-05-11-10-18-21.png)

# KSelection

首先分析数据的分布情况.  
![神经元激活的比例(前5个),ac为tanh](/blog_images/2019-05-11-10-23-55.png)  
平均占比也有0.98.且采用logistic regression也取得了不错的结果.由此可见有很大一块区域为线性的.

首先使用d=0.2的KTanh做尝试.  
![confusion matrix](/blog_images/2019-05-11-10-47-17.png)
![loss](/blog_images/2019-05-11-11-25-07.png)
![前5给神经元的k值](/blog_images/2019-05-11-10-47-28.png)  


问题
![k值震荡](/blog_images/2019-05-11-10-57-35.png)![震荡神经元的t值](/blog_images/2019-05-11-10-59-07.png)

而在简单任务(sin x)的学习中的:  
![t](/blog_images/2019-05-11-11-02-56.png)![k](/blog_images/2019-05-11-11-03-03.png)
