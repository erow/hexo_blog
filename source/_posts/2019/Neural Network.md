---
title: Neural Network
date: 2019-01-27 10:13:54
tags: [ML, NN]
categories: [ML]
---
# 反向传播网络

反向传播是神经网络中求最优参数的一个运用梯度下降算法的应用。
![简化模型](/blog_images/NN.jpg)  
这是我总结的简化后的神经网络的模型。一个layer表示一个隐层。具体的计算过程可以参见andrew的ML，这里采用的符号z,a也是依据课程的介绍。
$$ 
 a_1^{(2)} = g(\Theta_{10}^{(1)}x_0 + \Theta_{11}^{(1)}x_1 + \Theta_{12}^{(1)}x_2 + \Theta_{13}^{(1)}x_3) \newline a_2^{(2)} = g(\Theta_{20}^{(1)}x_0 + \Theta_{21}^{(1)}x_1 + \Theta_{22}^{(1)}x_2 + \Theta_{23}^{(1)}x_3) \newline a_3^{(2)} = g(\Theta_{30}^{(1)}x_0 + \Theta_{31}^{(1)}x_1 + \Theta_{32}^{(1)}x_2 + \Theta_{33}^{(1)}x_3) \newline h_\Theta(x) = a_1^{(3)} = g(\Theta_{10}^{(2)}a_0^{(2)} + \Theta_{11}^{(2)}a_1^{(2)} + \Theta_{12}^{(2)}a_2^{(2)} + \Theta_{13}^{(2)}a_3^{(2)}) \newline $$
 在计算梯度的时候主要方法是 求偏导数和换元法。从上图可知$z$是连接不同层之间的节点。
 在计算导数的时候，采用Z作为参考点会简化计算。先换元，转换为关于$z$的表达式$J(z^l)$.可知$\frac{∂J(Θ)}{∂z^{l}} =\frac{∂J(Θ)}{∂z^{l+1}}\frac{∂z^{l+1}}{∂z^{l}}$. 而这一部分$\frac{∂z^{l+1}}{∂z^{l}}=Θ^{k^T}z^{l+1}.*g'(z^l)$正是反向传播的关键。 要求得参数的导数所需要的最后一步$\frac{∂z^{l+1}}{∂Θ^{l}_{ij}}=a^l_j$
 由于：
 $$ g′(z^{l})=a^l.∗ (1−a^l)$$
 因此
 $$\delta^l=\frac{∂z^{l+1}}{∂z^{l}}=Θ^{k^T}z^{l+1}.*a^l.∗ (1−a^l)$$
 $$\frac{∂J}{∂Θ^{l}}=\delta^{l+1}a^{l^T}$$

 # 参考
 [Neural Networks: Learning](https://www.coursera.org/learn/machine-learning/supplement/Bln5m/model-representation-i)