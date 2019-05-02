---
title: numpy 性能提升
date: 2019-02-18 9:53:54
tags: [ML, cnn]
categories: [ML]
---
# Copies and Views
视图共享数据，但不是同一个对象。而复制会重新分配内存，在实际中应该尽量避免复制操作。
```python
a = np.arange(12)
b = a            # no new object is created
```
b与a是完全等价的
## View or Shallow Copy
也可以称作浅拷贝，它创建了新的对象，但是使用了相同的数据。
```python
>>> a = np.arange(12)
>>> c = a.view()
>>> c is a
False
>>> c.base is a                        # c is a view of the data owned by a
True
>>> c.flags.owndata
False
>>>
>>> c.shape = 2,6                      # a's shape doesn't change
>>> a.shape
(3, 4)
>>> c[0,4] = 1234                      # a's data changes
>>> a
array([[   0,    1,    2,    3],
       [1234,    5,    6,    7],
       [   8,    9,   10,   11]])
```
Slicing an array returns a view of it:
```
>>> s = a[ : , 1:3]     # spaces added for clarity; could also be written "s = a[:,1:3]"
>>> s[:] = 10           # s[:] is a view of s. Note the difference between s=10 and s[:]=10
>>> a
array([[   0,   10,   10,    3],
       [1234,   10,   10,    7],
       [   8,   10,   10,   11]])
Deep Copy
The copy method makes a complete copy of the array and its data.

>>> d = a.copy()                          # a new array object with new data is created
>>> d is a
False
>>> d.base is a                           # d doesn't share anything with a
False
>>> d[0,0] = 9999
>>> a
array([[   0,   10,   10,    3],
       [1234,   10,   10,    7],
       [   8,   10,   10,   11]])
```
## Deep Copy

完全复制数据。耗时大。
```
>>> d = a.copy()                          # a new array object with new data is created
>>> d is a
False
>>> d.base is a                           # d doesn't share anything with a
False
>>> d[0,0] = 9999
>>> a
array([[   0,   10,   10,    3],
       [1234,   10,   10,    7],
       [   8,   10,   10,   11]])
```

## 判断是否发生了拷贝
`a.__array_interface__['data']`返回数据指针 , `a.flags.owndata` 是否拥有数据, `a.base is b.base` 检测父元素。经过测试，第三种方法最为有效。
```python
a= np.arange(36).reshape(6,6) #第二种方法失效，reshape返回一个view。
b= a[1,:] # 第一种方法失效
c=a[::2,::3]
d = c.reshape(2,3) 
d.base is a.base # False, 由于c在空间上不连续，导致reshape重新开辟一块空间。
```
#  选择合适的操作
## 选择
以下方式操作的都是view
```python
a[1:2, 3:6]    # 切片 slice
a[::2]         # 跳步
```
而下面会导致copy
```python
a_copy1 = a[[1,4,6], [2,4,6]]   # 用 index 选
a_copy2 = a[[True, True], [False, True]]  # 用 mask
a_copy4 = a[a[1,:] != 0, :]  # fancy indexing
```
np中提供了`np.take()`,`np.compress`方法，功能与上面类似，但是更加高效。
## 变形
numpy 提供了几种方法：`a.reshape()`,`a.shape=()`,`a.resize()`,`a.flatten()`,`a.ravel()`.  
其中`a.shape=()` 必返回视图。
而`a.flatten()`必返回一个拷贝，如果不是必要应该避免。  
而其他操作在有需要的时候复制。

## 赋值
就地操作 ` a+=2`  
一些方法带有out参数，可以直接将结果写入out变量从而避免copy。
```python
np.add(a, 1, out=a)    # 0.008843
```
## 复制元素
`np.stride_tricks.as_strided`
这个方法可以起到分块的作用，同时也能改变矩阵的形状，大小，但是它返回的其实是一个view，因此十分高效。
首先把A看作是一维的，然后给出想要的形状，最后规定每一步的长度。
![](/blog_images/2019-02-18-13-28-18.png)  
比如im2col算法就有一个简单高效的实现：
```python
def im2col_3d(A, BSZ: tuple):
    # Parameters
    channel, r, c = A.shape
    s0, s1, s2 = A.strides
    nrows = r - BSZ[0] + 1
    ncols = c - BSZ[1] + 1
    shp = channel, BSZ[0], BSZ[1], nrows, ncols
    strd = s0, s1, s2, s1, s2

    out_view = np.lib.stride_tricks.as_strided(A, shape=shp, strides=strd)
    return out_view.reshape( channel * BSZ[0] * BSZ[1], -1)
```


# Trick
前面提到如果数组在内存空间中不连续，那么在一些操作就无法在原来的地方进行。如reshape。但是np视乎做过一些优化，如果在切片的步长是一个整数，那么就可以将其看作是连续的。
```
a= np.arange(72).reshape(12,6) 
c=a[::2,::3]
c.shape=12 #出错
e=a.ravel()[::3]
f1=e[::4]
f2=e[1::4]
f1.shape=2,3
f1.base is a.base # True
```
那么对其进行2次定长切片处理就能得到我们想要的东西。

# 疑问
```python
img = np.random.random((100, 3, 32, 32))
w = np.random.random((3, 5, 5))


def im2col_4d(A, BSZ: tuple):
    # Parameters
    m, channel, r, c = A.shape
    s0, s1, s2, s3 = A.strides
    nrows = r - BSZ[0] + 1
    ncols = c - BSZ[1] + 1
    shp = m, channel, BSZ[0], BSZ[1], nrows, ncols
    strd = s0, s1, s2, s3, s2, s3
    out_view = np.lib.stride_tricks.as_strided(A, shape=shp, strides=strd)
    return out_view.reshape(m, channel * BSZ[0] * BSZ[1], -1)


def im2col_3d(A, BSZ: tuple):
    # Parameters
    channel, r, c = A.shape
    s0, s1, s2 = A.strides
    nrows = r - BSZ[0] + 1
    ncols = c - BSZ[1] + 1
    shp = channel, BSZ[0], BSZ[1], nrows, ncols
    strd = s0, s1, s2, s1, s2

    out_view = np.lib.stride_tricks.as_strided(A, shape=shp, strides=strd)
    return out_view.reshape( channel * BSZ[0] * BSZ[1], -1)

def c1():

    value = np.zeros((100, 28, 28))
    for i in range(100):
        for c in range(3):
            # 转列 88ms
            col = im2col_sliding_strided(img[i, c, ...], (5, 5))
            # dot 用时30ms
            value[i, ...] += w[c].ravel().dot(col).reshape(28, 28)
    return value


def c2():
    # 高维数组的乘法效率很低，这个方法反而是最慢的
    # 355ms 这个方法也变慢了我感觉很意外
    col = im2col_4d(img, (5, 5))
    # dot 用时150ms
    return w.ravel().dot(col).reshape(-1, 28, 28)

def c3():
    # 使col仍为二维，效率最高
    value = np.zeros((100, 28, 28))
    for i in range(100):
        # 66 ms
        col = im2col_3d(img[i, ...], (5, 5))
        # 20ms
        value[i, ...] = w.ravel().dot(col).reshape(28, 28)
    return value

print(use_time(c1,10))
print(use_time(c2,10))
print(use_time(c3,10))
```
以上代码实现卷积运算时发现将所有数据统一处理反而是最慢的。结果表明c3,每次处理一组数据是最快的。其中大数组的`reshape`慢于多个小数组的`reshape`，同样点积也是大的慢。

## 
# Refences
[为什么用 Numpy 还是慢, 你用对了吗?](https://morvanzhou.github.io/tutorials/data-manipulation/np-pd/4-1-speed-up-numpy/)  
[Tutorial](https://docs.scipy.org/doc/numpy-1.14.5/user/quickstart.html)
[高效分块操作](https://blog.csdn.net/shwan_ma/article/details/78244044)