---
layout:     post
title:      Python中numpy.bincount()的用法
date:       2020-04-30
author:     AspenStars
header-img: img/post-bg-map.jpg
catalog: true
tags:
    - Python
    - numpy
    - 官方文档解读
---
本文参考了[Python的官方文档](https://numpy.org/doc/stable/reference/generated/numpy.bincount.html?highlight=bincount)，主要是对常用方法进行分析，并对注意事项进行记录
### numpy.bincount
#### 作用
统计**非负整型数组**中每个元素的出现次数
返回一个结果数组，结果数组的**索引$i$代表要统计数组的值**，结果数组的值代表$i$出现的次数
**Note**：输入数组只能是非负整数，权重可以是小数，但shape要相同

#### 函数原型
```python
numpy.bincount(x, weights=None, minlength=0)
```

#### 参数
- `x`:**一维非负整型**数组
- `weights`:（可选）与`x`的`shape`相同，用来代表**对应位置**的权重
- `minlength`:（可选）输出结果的**最小长度**（实际长度可以超过这个值，但绝不低于）

#### 返回值
返回一个结果数组，结果数组的**索引$i$代表要统计数组的值**，结果数组的值代表$i$出现的次数

结果数组的长度是[`x`中最大值+1] or [`minlength`]中的最大值

#### 举例
```python
np.bincount(np.array([0, 1, 1, 3, 2, 1, 7]))

out: array([1, 3, 1, 1, 0, 0, 0, 1])
```

```python
np.bincount(np.array([1, 1, 3, 2, 1, 7]))
out: array([0, 3, 1, 1, 0, 0, 0, 1])
```

```python
np.bincount(np.array([1, 1, 3, 2, 1, 7]), minlength=10)
out: array([0, 3, 1, 1, 0, 0, 0, 1, 0, 0])
```

```python
w = np.array([0.3, 0.5, 0.2, 0.7, 1., -0.6]) # weights
x = np.array([0, 1, 1, 2, 2, 2])
np.bincount(x,  weights=w)
out: array([ 0.3,  0.7,  1.1])
```


