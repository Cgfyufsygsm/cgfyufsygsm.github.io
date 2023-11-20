---
title: UCB CS61A 学习笔记
date: 2023-11-20 19:21:36
tags:
  - 笔记
  - Python
categories: 笔记
---

## 前言

感觉这学期没有学什么和写代码相关的课。于是加训一下之前自己不会的 Python。

备忘录，给自己看的。

## Lecture 1 & Lab 0

Python 可以直接从 MS Store 安装。

除号有两种：`/` 和 `//`，后者会向**下**取整。例如

```Python
>>> 7 // 9
0
>>> -7 // 9
-1
>>> 7 / 9
0.7777777777777778
```

相应地，取模运算严格遵照带余除法的定义：

```Python
>>> -7 % 9
2
>>> 7 % 9
7
>>> 7 % -9
-2
>>> -7 % -9
-7
```

变量定义的时候不需要显式声明类型，如可以直接写 `x = 3 + 5`，并且语句的末尾不需要加分号。

用 Python 写函数的时候可以通过在函数体第一行用一对三个单引号或三个双引号来定义 docstring（文档字符串），相当于起一个注释作用：

```Python
def twenty_twenty_three():
    """Come up with the most creative expression that evaluates to 2023,
    using only numbers and the +, *, and - operators.

    >>> twenty_twenty_three()
    2023
    """
    return 2023
```

> 用这个课程给的 `ok` 文件可以很方便的自测作业的正确性，具体使用方法可以看课程说明。