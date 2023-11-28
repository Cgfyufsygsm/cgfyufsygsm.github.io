---
title: 数学基础辅差
date: 2023-11-21 20:05:03
tags:
  - 高等数学
  - 笔记
  - 线性代数
categories: [笔记]
hide: false
---

## 向前-向后数学归纳法

某种比较神秘的数学归纳法变体。形式如下：

当一个命题满足如下条件时：

1. 命题关于无穷多个自然数成立；
2. 当 $n=k+1$ 时命题成立可以推知 $n=k$ 时命题成立。

则命题对全体自然数成立。

比较常见的想法是，证明命题对于 $n=2^k$ 成立，然后证明 $k+1\implies k$。比如均值不等式：

$$
\frac{1}{n}\sum_{i=1}^na_i\ge \left( \prod_{i=1}^na_i \right)^{\frac{1}{n}} 
$$

其中 $a_i > 0$，当且仅当 $a_1=a_2=\cdots=a_n$ 时等号成立。

> 证明：
> 
> $n=2$ 时，$a+b\ge 2\sqrt{ab}$ 略。
> 
> - 假设命题对于 $n=2^k$ 成立，考虑 $n=2^{k+1}$ 时：
>   $$
>   \frac{1}{2^{k+1}}\sum_{i=1}^{2^{k+1}}a_i = \frac{1}{2}\left( \frac{1}{2^k}\sum_{i=1}^{2^k}a_i+\frac{1}{2^k}\sum_{i=2^k+1}^{2^{k+1}}a_i \right) \ge \frac{1}{2}\left( \sqrt[2^k]{\prod_{i=1}^{a^k}a_i} + \sqrt[2^k]{\sum_{i=2^k+1}^{2^{k+1}}a_i}\right) \ge \left( \prod_{i=1}^{2^{k+1}} a_i\right) ^{\frac{1}{2^{k+1}}}
>   $$
> - 假设命题对于 $n=k+1$ 成立，考虑 $n=k$ 时（$k\ge 3$）
>   $$
>   \frac{1}{k}\sum_{i=1}^k a_i = \frac{1}{k+1}\left( \sum_{i=1}^ka_i + \frac{1}{k}\sum_{i=1}^ka_i \right) \ge \left( \left( \frac{1}{k}\sum_{i=1}^ka_i \right) \prod_{i=1}^k  a_i \right)^{\frac{1}{k+1}} 
>   $$
>   两边同时取 $k+1$ 次方，然后将 $\displaystyle \frac{1}{k}\sum_{i=1}^ka_i$ 约分，开 $k$ 次方便得到
>   $$
>   \frac{1}{k}\sum_{i=1}^ka_i\ge \left( \prod_{i=1}^ka_i \right) ^{\frac{1}{k}}
>   $$
>   
> 故命题对于 $n\ge 2$ 均成立，证毕。

## 双曲函数

定义：

$$
\sinh x = \frac{e^x-e^{-x}}{2}\\
\cosh x = \frac{e^{x}+e^{-x}}{2}\\
\tanh x = \frac{e^{x}-e^{-x}}{e^{x}+e^{-x}}
$$

- $\sinh x$ 为奇函数，单调增，值域 $\mathbb{R}$。
- $\cosh x$ 为偶函数，先减后增，值域 $[1,+\infty)$。
- $\tanh x$ 为奇函数，单调增，值域 $(-1,1)$。

有一些神奇的恒等式：

- $\cosh^{2}x-\sinh^{2}x=1$
- $\sinh'x = \cosh x$
- $\cosh'x = \sinh x$

两角和与差的公式和三角函数的略有不同。

- $\sinh(x\pm y) = \sinh x\cosh y \pm \cosh x \sinh y$
- $\cosh(x\pm y) = \cosh x \cosh y {\color{red}{\pm}} \sinh x \sinh y$，注意此处的区别。
- $\tanh(x\pm y) = \displaystyle \frac{\tanh x \pm  \tanh y}{1{\color{red}{\pm}} \tanh x \tanh y}$
- $\sinh 2x = 2\sinh x \cosh x$
- $\cosh 2x = \sinh^2x + \cosh^2x= 2\cosh^2x - 1 = 1 + 2\sinh ^2x$

主要应用是可以拿来换元：

- $\operatorname{arsinh}x = \ln\left( x+\sqrt{x^{2}+1} \right)$
- $\operatorname{arcosh}x = \ln\left( x+\sqrt{x^{2}-1} \right)$

拿来处理形如 $\sqrt{x^{2}+a^{2}}$ 的东西，可以令 $x = \sinh t$ 或 $\cosh t$。比如说：

$$
\int \frac{\mathrm{d}x}{\sqrt{x^{2}+1}}=\int \frac{\cosh t \mathrm{d}t}{\cosh t} = t + C = \operatorname{arsinh} x + C
$$