---
title: 一些高数积分笔记
date: 2023-11-03 21:33:48
tags:
  - 高等数学
  - 笔记
categories: [笔记, 本科课程]
---

救救期中。。

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
- $\cosh(x\pm y) = \cosh x \cosh y \pm \sinh x \sinh y$，注意此处的区别。
- $\tanh(x\pm y) = \displaystyle \frac{\tanh x \pm  \tanh y}{1\pm \tanh x \tanh y}$
- $\sinh 2x = 2\sinh x \cosh x$
- $\cosh 2x = \sinh^2x + \cosh^2x= 2\cosh^2x - 1 = 1 + 2\sinh ^2x$

主要应用是可以拿来换元：

- $\operatorname{arsinh}x = \ln\left( x+\sqrt{x^{2}+1} \right)$
- $\operatorname{arcosh}x = \ln\left( x+\sqrt{x^{2}-1} \right)$

拿来处理形如 $\sqrt{x^{2}+a^{2}}$ 的东西，可以令 $x = \sinh t$ 或 $\cosh t$。比如说：

$$
\int \frac{\mathrm{d}x}{\sqrt{x^{2}+1}}=\int \frac{\cosh t \mathrm{d}t}{\cosh t} = t + C = \operatorname{arsinh} x + C
$$

## 三角函数的一些公式

$\tan^{2}x+1=\sec^{2}x$ 是很重要的关于 $\tan x$ 的处理，很多时候和 $\mathrm{d}\tan x = \sec^{2} x \mathrm{d}x$ 一起处理问题。

然后是三角函数的 Reduction Formula，采用分部积分来处理。

$$
\begin{aligned}
\int\sin^m x \mathrm{d}x =&-\int \sin^{m-1}x\mathrm{d}\cos x\\
=&\int \cos x \mathrm{d} \sin^{m-1}x - \cos x \sin^{m-1}x\\
=&(m-1)\int\sin^{m-2}x \cos^{2}x\mathrm{d}x - \cos x \sin^{m-1}x\\
=&(m-1)\int\sin^{m-2}x(1-\sin^{2}x)\mathrm{d}x - \cos x\sin^{m-1}x\\
m \int \sin^m x \mathrm{d}x=& (m-1)\int \sin^{m-2}x\mathrm{d}x-\sin^{m-1}x\cos x\\
\int \sin^m x\mathrm{d}x =& \frac{m-1}{m}\int \sin^{m-2}x \mathrm{d}x - \frac{1}{m}\sin^{m-1}x\cos x
\end{aligned}
$$

$$
\begin{aligned}
\int \cos^mx \mathrm{d}x &= \int \cos^{m-1}x\mathrm{d}\sin x \\
&= \sin x \cos^{m - 1}x - \int \sin x \mathrm{d}\cos^{m - 1}x\\
&= \sin x \cos^{m - 1}x + (m - 1) \int \sin^{2}x\cos^{m-2}\mathrm{d}x\\
&= \sin x \cos^{m - 1}x + (m - 1) \int (1-\cos^{2}x)\cos^{m-2}\mathrm{d}x\\
&= \sin x \cos^{m - 1}x + (m - 1) \int \cos^{m-2}\mathrm{d}x-(m-1)\int\cos^m\mathrm{d}x\\
m \int \cos ^m x\mathrm{d}x&=(m-1)\cos^{m-2}\mathrm{d}x + \sin x \cos^{m - 1}x\\
\int \cos^{m}\mathrm{d}x &= \frac{m - 1}{m}\int \cos^{m - 2}\mathrm{d}x + \frac{1}{m}\sin x \cos^{m - 1}x
\end{aligned}
$$

$$
\begin{aligned}
I_m=\int \tan^m x \mathrm{d}x &= \int \tan^{m - 2}x(\sec^{2}x - 1) \mathrm{d}x\\
&= \int \tan ^{ m - 2}x \mathrm{d}\tan x - \int \tan^{m - 2}x\mathrm{d}x\\
&= \tan^{m - 1}x - \int \tan x \mathrm{d} \tan^{m - 2}x - I_{m-2}\\
&= \tan^{m - 1}x- (m - 2)\int \tan^{m - 2}(1+\tan^{2}x)\mathrm{d}x - I_{m-2}\\
&= \tan^{m - 1}x - (m-1)I_{m-2} - (m - 2)I_m\\
I_m &= \frac{\tan^{m - 1}x}{m - 1} - I_{m - 2}
\end{aligned}
$$

## 一些比较重要的

好恶心啊。

- $\displaystyle \int \frac{\mathrm{d}x}{a^{2}-x^{2}}$
- $\displaystyle \int \frac{\mathrm{d}x}{a^{2}+x^{2}}$
- $\displaystyle \int \frac{\mathrm{d}x}{\sqrt{a^{2}-x^{2}}}$，其中 $a>0$
- $\displaystyle \int\sqrt{a^{2}-x^{2}}\mathrm{d}x$，其中 $a>0$
- $\displaystyle \int \frac{\mathrm{d}x}{\sqrt{x^{2}+a^{2}}}$
- $\displaystyle \int \frac{\mathrm{d}x}{\sqrt{x^2-a^{2}}}$，其中 $a>0$
- $\displaystyle \int\sqrt{a^2+x^2}\mathrm{d}x$
- $\displaystyle \int\sqrt{x^{2}-a^{2}}\mathrm{d}x$

对于第一个，我们发现可以使用拆项的方式，其即为 $\displaystyle \int \frac{\mathrm{d}x}{a^{2}-x^{2}}=\frac{1}{2a}\ln\left\vert \frac{1-x}{1+x} \right\vert +C$。

对于第二个，发现是 $\arctan$ 的形式，凑微分便有 $\displaystyle \int \frac{\mathrm{d}x}{a^{2}+x^{2}}=\frac{1}{a}\arctan \frac{x}{a}+C$。

对于第三个，发现是 $\arcsin$ 的形式，凑微分便有 $\displaystyle \int \frac{\mathrm{d}x}{\sqrt{a^{2}-x^{2}}} = \arcsin \frac{x}{a} + C$。

对于 $\displaystyle \int\sqrt{a^{2}-x^{2}}\mathrm{d}x$，使用三角换元即可，不是特别恶心。令 $x=a\sin t \displaystyle \left( -\frac{\pi}{2}\le t\le\frac{\pi}{2} \right)$，则
$$
\begin{aligned}
\int\sqrt{a^{2}-x^{2}}\mathrm{d}x &= \int a\sqrt{1-\sin^{2}t}\cdot a\cos t \mathrm{d}t\\
&=a^2\int \cos^{2} \mathrm{d}t\\
&= \frac{a^{2}}{2}\int(1+\cos 2t)\mathrm{d}t\\
&= \frac{a^{2}}{2}\left( t+\sin t\cos t \right) +C\\
&=\frac{a^2}{2}\left[ \arcsin \frac{x}{a} + \frac{x}{a}\sqrt{1-\left( \frac{x}{a} \right) ^{2}} \right] +C\\
&= \frac{a^2}{2}\arcsin \frac{x}{a} + \frac{x}{2}\sqrt{a^{2}-x^{2}}+C
\end{aligned}
$$

对于 $\displaystyle \int \frac{\mathrm{d}x}{\sqrt{x^{2}+a^{2}}}$，同样可以使用三角换元，令 $\displaystyle x=a \tan t \left( -\frac{\pi}{2}< t < \frac{\pi}{2} \right)$，则
$$
\begin{aligned}
\int \frac{\mathrm{d}x}{\sqrt{x^{2}+a^{2}}} &= \int \frac{a \sec^{2}t \mathrm{d} t}{\sqrt{a^{2}\sec^{2} t}}= \int \sec t \mathrm{d}t\\
\end{aligned}
$$

> 在继续之前，我们先处理 $\sec$ 的积分，有 $\displaystyle \int \sec x \mathrm{d}x = \int \frac{\cos x}{\cos^{2}x}\mathrm{d}x = \int \frac{\mathrm{d}(\sin x)}{1-\sin^{2}x} = \frac{1}{2}\ln\left\vert \frac{1+\sin x}{1 - \sin x} \right\vert + C$，事实上还可以继续化简，继续上下配凑：$\displaystyle \frac{1}{2}\ln\left\vert \frac{1+\sin x}{1-\sin x} \right\vert  = \frac{1}{2}\ln\left\vert \frac{(1+\sin x)^2}{\cos^{2}x} \right\vert = \ln\left\vert \frac{\sin x + 1}{\cos x} \right\vert =\ln\left\vert \tan x + \sec x \right\vert$，综上有 $\displaystyle \int \sec x \mathrm{d}x = \ln \left\vert \tan x + \sec x \right\vert + C$。

回到上面，我们继续处理：

$$
\int \frac{\mathrm{d}x}{\sqrt{x^{2}+a^{2}}}=\ln\left\vert \tan t + \sec t \right\vert + C
$$

在直角三角形中，可以看出来，当 $\displaystyle \tan t = \frac{x}{a}$ 时，$\displaystyle \sec t = \frac{\sqrt{x^{2}+a^{2}}}{a}$，故

$$
\int \frac{\mathrm{d}x}{\sqrt{x^{2}+a^{2}}}=\ln\left\vert \frac{x}{a}+\frac{\sqrt{x^{2}+a^{2}}}{a} \right\vert +C = \ln\left\vert x+\sqrt{x^{2}+a^{2}} \right\vert +C
$$

（最后一步将 $\ln a$ 提出来扔进 $C$ 里面了），和上文的双曲换元比较，谁简单应该不用我说了。

接下来是 $\displaystyle \int \frac{\mathrm{d}x}{\sqrt{x^{2}-a^{2}}}$，可以类似上文，当 $x > a$ 时令 $x = a\sec t$，然后一通操作，再分类讨论 $x < -a$ 的情况，好麻烦啊，本文略了。但是既然这让我们这么痛苦，为什么不考虑一下双曲换元呢？令 $x > a$ 时 $x = a\cosh t (t>0)$，则

$$
\begin{aligned}
\int \frac{\mathrm{d}x}{\sqrt{x^{2}-a^{2}}}&= \int \frac{\sinh t \mathrm{d}t}{\sinh t} = t = \operatorname{arcosh}\frac{x}{a} +C= \ln(x+\sqrt{x^{2}-a^{2}}) + C\\
\end{aligned}
$$

对于 $x<-a$ 的讨论是类似的，上下的负号会被消去，最后都是一样的。所以 $\displaystyle \int \frac{\mathrm{d}x}{\sqrt{x^{2}-a^{2}}} = \ln \left\vert x+\sqrt{x^{2}-a^{2}} \right\vert +C$

综上我们有很常见且重要的结论：

$$
\int \frac{\mathrm{d}x}{\sqrt{x^{2}\pm a^{2}}}=\ln\left\vert x+\sqrt{x^{2}\pm a^{2}} \right\vert +C
$$

最后是 $\displaystyle \int\sqrt{x^{2}+a^{2}}\mathrm{d}x$ 和 $\displaystyle \int \sqrt{x^{2}-a^{2}}\mathrm{d}x$，这两个既可以使用分部积分也可以使用双曲换元，这里两种思路都说一遍。

先看前者，我们直接分部积分莽的话，有

$$
\begin{aligned}
\int\sqrt{x^{2}+a^{2}}\mathrm{d}x&=x\sqrt{x^{2}+a^{2}}-\int x\mathrm{d}\sqrt{x^{2}+a^{2}} \\
&= x\sqrt{x^{2}+a^{2}} - \int \frac{x^{2}\mathrm{d}x}{\sqrt{x^{2}+a^{2}}}\\
&= x\sqrt{x^{2}+a^{2}} - \int \sqrt{x^{2}+a^{2}}\mathrm{d}x + a^{2}\int \frac{\mathrm{d}x}{\sqrt{x^{2}+a^{2}}}\\
&= \frac{x}{2}\sqrt{x^{2}+a^{2}}+\frac{a^{2}}{2}\ln\left\vert x+\sqrt{x^{2}+a^{2}} \right\vert +C
\end{aligned}
$$

或者我们换元，令 $x = a\sinh t$，则

$$
\begin{aligned}
\int\sqrt{x^{2}+a^{2}}\mathrm{d}x &= a^{2}\int \cosh^{2}t \mathrm{d}t \\
&= a^2\int \frac{\cosh 2t+1}{2}\mathrm{d}t\\
&= \frac{a^2}{4}\int \cosh 2t \mathrm{d}(2t) + \frac{a^2}{2}t\\
&= \frac{a^2}{4}\sinh 2t + \frac{a^{2}}{2}t+C\\
&= \frac{a^2}{2}\sinh t \cosh t + \frac{a^{2}}{2}t+C\\
&= \frac{a}{2}x\sqrt{1+\frac{x^{2}}{a^{2}}}+\frac{a^{2}}{2}\ln\left\vert \frac{x}{a}+\sqrt{\left( \frac{x}{a} \right) ^{2}+1} \right\vert +C\\
&= \frac{x}{2}\sqrt{x^{2}+a^{2}}+\frac{a^{2}}{2}\ln\left\vert x+\sqrt{x^{2}+a^{2}} \right\vert +C
\end{aligned}
$$

再看后者；

$$
\begin{aligned}
\int\sqrt{x^{2}-a^{2}} \mathrm{d}x &= x\sqrt{x^{2}-a^{2}}-\int x\mathrm{d}\sqrt{x^{2}-a^{2}}\\
&= x\sqrt{x^{2}-a^{2}}-\int \frac{x^{2}}{\sqrt{x^{2}-a^{2}}}\mathrm{d}x\\
&= x\sqrt{x^{2}-a^{2}}-\int \sqrt{x^{2}-a^{2}}\mathrm{d}x - a^{2}\int \frac{\mathrm{d}x}{\sqrt{x^{2} - a^{2}}}\\
&= \frac{x}{2}\sqrt{x^{2}-a^{2}}- \frac{a^{2}}{2}\ln\left\vert x+\sqrt{x^{2}-a^{2}} \right\vert +C\\
\end{aligned}
$$

好像根本就差不多啊，然后一样，分讨，先考虑 $x>a$，令 $x = a\cosh t$，则

$$
\begin{aligned}
\int \sqrt{x^{2} - a^{2}}\mathrm{d}x &= a^2\int \sinh^2 t \mathrm{d}t\\
&= a^{2}\int \frac{\cosh2t-1}{2}\mathrm{d}t\\
&= -\frac{a^{2}}{2}t + \frac{a^{2}}{4}\sinh 2t + C\\
&= -\frac{a^{2}}{2}t + \frac{a^{2}}{2}\sinh t \cosh t + C\\
&= -\frac{a^{2}}{2}\ln\left( \frac{x}{a}+\sqrt{\left( \frac{x}{a} \right) ^{2}-1} \right) +\frac{a}{2}x\sqrt{\left( \frac{x}{a} \right) ^{2}-1}+C\\
&= \frac{x}{2}\sqrt{x^{2}-a^{2}}-\frac{a^2}{2}\ln\left( x+\sqrt{x^{2}-a^{2}} \right) +C
\end{aligned}
$$

然后还要对于 $x<-a$ 讨论，此处略过，同样是负号没了，所以答案和上面一样的，注意绝对值的问题。

也可以总结一下，但是用的不太多：

$$
\int \sqrt{x^{2}\pm a^{2}}\mathrm{d}x = \frac{x}{2}\sqrt{x^{2}\pm a^{2}}\pm \frac{a^2}{2}\ln\left\vert x + \sqrt{x^{2}\pm a^{2}} \right\vert +C
$$

还有一个特别恶心的递推：$\displaystyle I_n=\int \frac{\mathrm{d}t}{\left( t^{2}+a^{2} \right) ^{n}}$，期中 $n$ 为大于 $1$ 的整数，且 $a>0$。

## 定积分的一些内容

分部积分的一个递推：考虑我们要求 $\displaystyle I_n = \int_0^{\frac{\pi}{2}}\sin^nx \mathrm{d}x$，其中 $n$ 为大于 $1$ 的整数，则：

$$
\begin{aligned}
I_n &= -\int_0^{\frac{\pi}{2}}\sin^{n-1}x\mathrm{d}\cos x \\
&= \int_0^{\frac{\pi}{2}}\cos x \mathrm{d}\sin^{n-1}x - \sin^{n-1}x \cos x\big|_0^{\frac{\pi}{2}}\\
&= (n-1)\int_0^{\frac{\pi}{2}}\sin^{n-2}x \cos^{2}x\mathrm{d}x\\
&= (n-1)\int_0^{\frac{\pi}{2}}(1-\sin^{2}x)\sin^{n-2}x\mathrm{d}x\\
&=(n-1)I_{n-2}-(n-1)I_n\\
I_n&= \frac{n-1}{n}I_{n-2}
\end{aligned}
$$

而 $I_0 = \displaystyle \frac{\pi}{2}$，$I_1=1$，所以把递推式展开，有

$$
\int_0^{\frac{\pi}{2}}\sin^{2k}x\mathrm{d}x=\frac{(2k-1)!!}{(2k)!!}\cdot \frac{\pi}{2}\\
\int_0^{\frac{\pi}{2}}\sin^{2k+1}x\mathrm{d}x=\frac{(2k)!!}{(2k+1)!!}
$$

其中 $k=1,2, \cdots$。

并且由于若我们令 $t = \displaystyle \frac{\pi}{2}-x$，则

$$
\int_0^{\frac{\pi}{2}}\cos^nx\mathrm{d}x=\int _{\frac{\pi}{2}}^0\sin^n t \mathrm{d}(-t) = \int_0^{\frac{\pi}{2}}\sin^nx\mathrm{d}x
$$

所以上面的递推式的形式对 $\cos$ 完全适用。