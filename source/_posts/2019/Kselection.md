---
title: KSelection
date: 2019-05-5 12:26:01
tags: ［构想］
categories: [ML]
---

如果把神经网络分为**选择**与**重构**两个部分,神经网络能够不断剔除无用信息(针对任务),保留有效信息,从而归结出结论(识别或者回归).那么可不可以通过修改选择来提升网络性能?

# KSelection
在神经网络激活函数前增加一系数k:
$$g(k*Z),Z = w x +b$$
假设g为sigmoid.k越大,则其有效区域越小;反之越大.
$$ len=\frac{8}{k*w}, center = -b/w$$
k可以改变有效区域长度,而不改变其中心.

# 选择部分有用信息
如果眼前的图像太小怎么办呢?放大!
![](/blog_images/2019-05-05-13-40-54.png)

我假设一组数据中只有20%是有用的. 那么可以通过修改k的值,修改数据的分布,使其20%落在有效区域上.
```python
def f(x):
    res = x.clone()
    t = x[x >= 0.2]
    res[x >= 0.2] = 1 + 1*(t - 0.2) ** 3
    t = x[x < 0.2]
    res[x < 0.2] = 1 + 20*(t - 0.2) ** 3
```

# 实验

## 均有分布
```python
def efficiency_range(linear):
    with torch.no_grad():
        return (-linear.bias) / linear.weight.flatten()

def showNet(model):
    X = torch.linspace(-1,1,1000).reshape(-1,1)
    y = model(X)
    plt.plot(X.numpy(), y.detach().numpy())

mid =60
net = nn.Sequential(
    nn.Linear(1,mid),
    nn.Sigmoid(),
    nn.Linear(mid,1)
)

net1 = nn.Sequential(
    nn.Linear(1,mid),
    SAAC(mid),
    nn.Linear(mid,1)

)

X = torch.linspace(-1,1,1000).reshape(-1,1)

plt.plot(X.numpy(),(torch.sin(10*X)).numpy())

loss_fun = nn.MSELoss()
opt = optim.Adam(net.parameters())
rec=[]
for i in range(10001):
    X = torch.linspace(-1,1,1000).reshape(-1,1)
    Y = torch.sin(10*X)
    pred = net(X)
    loss = loss_fun(pred,Y)
    opt.zero_grad()
    loss.backward()
    opt.step()
    if i % 100==0:
        print(f'{i}-loss: {loss}')
        rec.append(loss.item())

showNet(net)
rec1=[]
loss_fun = nn.MSELoss()
opt = optim.Adam(net1.parameters())
for i in range(2001):
    X = torch.linspace(-1,1,200).reshape(-1,1)
    Y = torch.sin(10*X)
    pred = net1(X)
    loss = loss_fun(pred,Y)
    opt.zero_grad()
    loss.backward()
    opt.step()
    if i % 100==0:
        print(f'{i}-loss: {loss}')
        rec1.append(loss.item())

showNet(net1)
plt.legend(['sin(10x)','net','net1'])
```
![](/blog_images/2019-05-05-13-54-29.png)![loss,蓝色为普通方法](/blog_images/2019-05-05-13-53-32.png)
使用KSelection后提升了5倍的速度,取得了同样的结果.

## 有偏样本
样本不再是[-1,1]之间的均有分布,而是`distributions.normal.Normal(0,1)`的正态分布.两个网络用同样的方法训练10001次  
![](/blog_images/2019-05-05-14-16-47.png)![](/blog_images/2019-05-05-14-17-01.png)  
可以看到,用普通的方法网络无法收敛,而KSelection则缓慢地收敛.

## 使用更少的神经元
将隐层神经元改为10.  
![](/blog_images/2019-05-05-14-33-57.png)![](/blog_images/2019-05-05-14-34-06.png)

