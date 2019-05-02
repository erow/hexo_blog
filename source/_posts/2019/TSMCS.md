---
title: Neuroplasticity Network
date: 2019-04-19 9:53:54
tags: [ML, 遗传算法]
categories: [ML]
---

# 问题描述
在一个100*100的虚拟环境中有960个有机体和50个食物.每隔50步在区域内随机均匀生成50个新食物,同时旧的食物会消失.而有机体由大脑模型和更新模型组成,能够感知最近的食物和移动,同时它会消耗能量需要不断进食来补充能量.通过遗传算法来筛选活的最好的个体,并最终希望得到一个能够良好运作的决策系统(brain model)和能够适应环境,改变决策的更新规则(plasticity model).
# Brain Model
由一个类似[hopfield network](https://blog.csdn.net/silent56_th/article/details/68066752)的网络组成.其中有两个位置传感器输入,两个上一次动作输入,以及3个内部神经元,和2个输出神经元,共九个神经元.初始神经元为全零.此外不同于一般的神经网络,这里使用9x9的bias.因此可以表示为`r1=torch.sigmoid(r.matmul(w)+b.sum(0))`,使用增广矩阵可以提升效率
```python
re=torch.cat([r,ones([m,9])],1)
we=torch.cat([w,b],0)
r2=sigmoid(re.matmul(we))
```
此外该网络并不一定是全连接的,由一个9x9的**连接矩阵M**表示是否有连接.还有一个4x1的**输入系数矩阵k**对输入归一化.

# Plasticity Model
这里希望习得一个能够更新权值的规则.在前向神经网络中,一般可以对损失函数进行求导,进而使用BP来更新权值.而在这个问题中,很难设计出一个合理的损失函数来求导,那么怎么计算$\Delta w$呢?干脆设计一个可以计算$\Delta w$的网络,然后交给遗传算法来判断.
![](/blog_images/BA8@O2[OOQDA655_Y8~{YZU.png)

# Farm
假设这些有机体生活在一个100x100的农场里.这些有机体在农场里生活竞争,然后繁殖进化,最后得到强大的个体.

