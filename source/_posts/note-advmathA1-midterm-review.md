---
title: 高数 A (I) 期中复习
date: 2023-11-06 13:30:01
tags: 
  - 高等数学
  - 笔记
categories: [笔记, 本科课程]
password: 20041031
---

## Notes
### 柯西命题

若 $\{ a_n \}$ 收敛到 $l$，则
$$
\lim_{n \to \infty} \frac{\sum_{k=1}^na_k}{n}=l
$$

相当于平均值也收敛到 $l$。怎么考虑呢？当 $n$ 充分大的时候，后面的都充分接近 $l$，就把前面不好的部分平均掉了。下面写一下严谨证明。

即我们希望，对于 $\forall \varepsilon>0$，都存在一个 $N$ 使得 $\forall n>N$，有
$$
\left\vert \lim_{n \to \infty}\frac{\sum_{k=1}^na_k}{n}-l \right\vert = \left\vert \lim_{n \to \infty}\frac{\sum_{k=1}^n(a_k-l)}{n} \right\vert  < \varepsilon
$$

由三角不等式，我们只需要

$$
\left\vert \lim_{n \to \infty}\frac{\sum_{k=1}^n(a_k-l)}{n} \right\vert \le \frac{1}{n}\sum_{k=1}^n|a_k-l| < \varepsilon
$$

那么我们就假设存在这样一个 $M$，使得 $M$ 之后的充分接近 $\varepsilon$，可以把前面糟糕的部分拯救回去，取一 $M$ 使得 $\forall n>M$ 有 $\left\vert a_n-l \right\vert <\displaystyle \frac{\varepsilon}{2}$，则

$$
\begin{aligned}
\frac{1}{n}\sum_{k=1}^n|a_k-l| &= \frac{1}{n}\sum_{k=1}^M|a_k-l| + \frac{1}{n}\sum_{k=M+1}^n|a_k-l| \\
&< \frac{1}{n}\sum_{k=1}^M|a_k-l| + \frac{(n-M)\varepsilon}{2n}\\
&<\frac{1}{n}\sum_{k=1}^M|a_k-l| + \frac{\varepsilon}{2}
\end{aligned}
$$

我们让

$$
\frac{1}{n}\sum_{k=1}^M|a_k-l| \le \frac{\varepsilon}{2}
$$

即可，所以取

$$
N=  \max\left\{ \frac{2\sum_{k=1}^M|a_k-l|}{\varepsilon},M \right\} 
$$

时其就能成立。

### 间断点的分类

假定 $f(x)$ 在点 $a$ 的一个空心邻域内有定义，但 $\displaystyle \lim_{x \to a}f(x)$ 不存在，考虑进行分类：

- 可去间断点：$\displaystyle \lim_{x \to a+0}f(x)$ 和 $\displaystyle \lim_{x \to a-0}f(x)$ 存在且相等但不等于 $f(a)$。即修改 $f(a)$ 的值可以使得其连续，所以叫“可去”。
- 跳跃间断点：$\displaystyle \lim_{x \to a+0}f(x)$ 和 $\displaystyle \lim_{x \to a-0}f(x)$ 存在**但不相等**，相当于函数值发生了突变，所以叫跳跃间断点。
- 第二类间断点：$\displaystyle \lim_{x \to a+0}f(x)$ 和 $\displaystyle \lim_{x \to a-0}f(x)$ **至少有一个不存在**，比如说 $\displaystyle \sin \frac{1}{x}$ 在 $x=0$ 处高频振荡的行为。


### 迷思

考虑定义在一段区间 $(a,b)$ 上的 $f(x)$，问：$f(x)$ 严格单调是否为其存在反函数 $f ^{-1}(x)$ 的**必要**条件，是的话请证明，不是的话请举出反例。

而若 $f(x)\in C(a,b)$，则此时 $f(x)$ 严格单调是否为其存在反函数 $f ^{-1}(x)$ 的**充要**条件，是的话请证明，不是的话请举出反例。（做不出来的话可以参考[这里](https://zhuanlan.zhihu.com/p/657255392)）

### D(x) 和 R(x) 的连续性

考虑定义在 $(0,1)$ 上的 Dirichlet 函数 $\displaystyle D(x) = \begin{cases} 1 & x\in \mathbb{Q}  \\ 0 & x \not\in \mathbb{Q} \end{cases}$ 和 Riemann 函数 $\displaystyle R(x) = \begin{cases} \frac{1}{q} & x=\frac{p}{q}\in \mathbb{Q} \\ 0 & x\not\in \mathbb{Q} \end{cases}$。

证明 $D(x)$ 处处不连续：取 $r\in (0,1)$，$\forall n\in \mathbb{N}^*$，由实数的稠密性知 $\exists$ 有理数 $r_n\in (r-n^{-1},r)$ 和无理数 $y_n\in (r-n^{-1}, r)$，显然 $\displaystyle \lim_{n \to \infty}r_n=\lim_{n \to \infty}y_n=r$，但是 $D(r_n) = 1\ne 0=D(y_n)$，根据归结原理 $\displaystyle \lim_{x \to r-0}D(x)$ 不存在，同理右极限也不存在，故处处间断。

证明 $R(x)$ 在有理数点不连续，在无理数点连续。先证明 $\displaystyle \lim_{x \to r}R(x) = 0$，然后就很自然了：考虑使用 $\varepsilon-\delta$ 语言，假定对于一个确定 的 $\varepsilon>0$，找一个足够大的整数 $n>\displaystyle \frac{1}{\varepsilon}$，取一个集合 $\displaystyle A=\left\{ y=\frac{p}{q}\middle| p<q,q\le n\right\}$，其显然为有限集。且 $\forall x\in (0,1)\backslash A$ 有 $f(x) < \displaystyle \frac{1}{n} < \varepsilon$。

取一个 $A$ 中元素离 $r$ 最近的，令距离为 $d$，即形式化地令 $d = \displaystyle \min_{y\in A,y\ne r}|y-r|$，显然 $d>0$，那么取 $\delta = \displaystyle \frac{d}{2}$，由于 $U_{\delta}(r)\cap A=\varnothing$，我们 $\forall x\in U_\delta(r)$ 有 $f(x) < \varepsilon$，证毕。

## Prob
1. 已知 $\displaystyle \lim_{x \to a}f(x) = l$。问：若已知 $\{ x_n \}$ 满足 $\displaystyle \lim_{n \to \infty}x_n = a$，是否可以推出 $\displaystyle \lim_{n \to \infty}f(x_n) = l$，可以则证明，不可以则给反例。
1. 计算极限
   (1) $\displaystyle \lim_{n \to \infty}\sqrt[3]{n}\left( \sqrt[3]{n+1}-\sqrt[3]{n} \right)$
   (2) $\displaystyle \lim_{n \to \infty}\sin^{2}\left( \pi\left( \sqrt{n^{2}+n}-n \right)  \right)$
   (3) $\displaystyle \lim_{n \to \infty}\frac{2^{n}n!}{n^{n}}$
   (4) $\displaystyle \lim_{x \to 0^+} \frac{1-\sqrt{ \cos x}}{x(1-\cos \sqrt{ x})}$
   (5) $\displaystyle \lim_{x \to 0} \frac{2\sin x- \sin 2x}{x^3}$
   (6) $\displaystyle \lim_{n \to \infty}\left[ (n+1)^\alpha - n^\alpha \right]$，其中 $\alpha\in(0,1)$。

1. $\{ a_n \}$，$S_n=\displaystyle \sum_{k=1}^na_k$。
   (1) 若 $S_n$ 收敛，证明 $\displaystyle \lim_{n \to \infty}a_n=0$
   (2) 若 $\displaystyle \lim_{n \to \infty}a_n=0$，则 $S_n$ 是否一定收敛？

1. 斐波那契数列 $\displaystyle F_n = \begin{cases} 0 & n=0  \\ 1 & n=1\\F_{n-1}+ F_{n-2}&n\ge2\end{cases}$ 的相邻两项之比 $\displaystyle x_n = \frac{F_{n+1}}{F_n} (n\ge 1)$ 是否存在极限？存在则求之。

1. 判断连续性：
   $$
   f(x) = \lim_{n \to \infty}\frac{\ln(e^n+x^n)}{n}\quad (x\ge 0)
   $$

1. 是否存在定义在 $\mathbb{R}$ 上且在任意闭区间无界的函数？

1. 设 $f(x)\in C[a,b]$，$x_i\in[a,b]$，$t_i>0~(i=1,2, \cdots n)$，且 $\displaystyle \sum_{i=1}^nt_i=1$，求证 $\exists \xi\in[a,b]$ 使得 $\displaystyle f(\xi)=\sum_{i=1}^nt_if(x_i)$。

1. $f(x)\in C(0,+\infty)$，若 $\forall x\in(0,+\infty)$ 有 $f(x) = f(x^3)$，已知 $f(112) = 1$，求 $f(1031)$。

1. $f(x)\in C[0,1]$ 且 $f(0) = f(1)$，证明 $\forall n\in \mathbb{N}^{*} ~\exists c\in[0,1]$ 使得 $f(c) = \displaystyle f\left( c+\frac{1}{n} \right)$。

1. 给定 $f(x):[a,b]\to[a,b]$ 满足 $\forall x,y\in [a,b]$ 有 $|f(x)-f(y)|\le k|x-y|$，其中 $k$ 为 $(0,1)$ 间的常数，依次完成下列问题：
   (1) 证明：$f(x)$ 在 $(a,b)$ 上连续；
   (2) 讨论 $kx - f(x)$ 的单调性；
   (3) 证明 $\exists c\in [a,b]$ 使得 $f(c) = c$；
   (4) 任取 $x_1 \in [a,b]$，定义 $x_{n+1} = \displaystyle \frac{1}{2}(x_n+f(x_n))$，证明 $\displaystyle \lim_{n \to \infty}x_n$ 存在。

1. $f(x)$ 在 $U_r(0)$ 上有定义，回答下列问题：
   (1) 若 $f'(0)$ 存在，证明 $\displaystyle \lim_{\Delta x \to 0}\frac{f(\Delta x) - f(-\Delta x)}{2\Delta x} = f'(0)$。
   (2) 若 $\displaystyle \lim_{\Delta x \to 0}\frac{f(\Delta x) - f(-\Delta x)}{2\Delta x}$ 存在，能否说明 $f'(0)$ 存在？

1. $f(x)$ 定义在 $\mathbb{R}$ 上，且 $f'(0)$ 存在，回答下列问题：
   (1) 若正序列 $\left\{ x_n \right\}$ 和负序列 $\left\{ y_n \right\}$ 均收敛至 $0$，证明 $\displaystyle \lim_{n \to \infty}\frac{f(x_n)-f(y_n)}{x_n-y_n}=f'(0)$。
   (2) 若两个正序列 $\left\{ x_n \right\}$ 和 $\left\{ z_n \right\}$ 均收敛至 $0$，问 $\displaystyle \lim_{n \to \infty}=\frac{f(x_n)-f(z_n)}{x_n-z_n}$ 是否总成立。

## Sol

1. 请注意我们在研究函数极限与数列极限的关系的时候，$\{ x_n \}$ **一定要在 $a$ 的空心邻域内取值**，因为我们定义函数极限的时候并没有用到 $f(a)$ 本身，你如果选的试探序列试探到了 $x=a$ 的话会有比较不好的结果。简单的反例就是 $\displaystyle f(x)=\begin{cases} 1 & x=a \\ 0& x\ne a \end{cases}$。

1. (1) 立方差公式
   (2) 把括号里面的有理化
   (3) 比较高档。感谢我的室友，可以这样，考虑 $n$ 为奇数的时候：
   $$
   \begin{aligned}
   \frac{2^nn!}{n^n} &= \frac{(2\times 1)(2\times 2)\cdots(2n)}{n^n} \\
   &= \frac{2}{n}\cdot \frac{4\times 2n}{n^2}\cdot \frac{6\times (2n-2)\cdots}{n^2\cdots}\\
   &\le \frac{2}{n}\cdot \left( \frac{(n+2)^{2}}{n^{2}} \right) ^{\frac{n-1}{2}}\\
   &=\frac{2}{n}\left( 1+\frac{1}{\frac{n}{2}} \right)^{\frac{n}{2(n-1)}} \\
   &\to \frac{2}{n}e^{\frac{1}{n-1}}\to \frac{2}{n}\to 0
   \end{aligned}
   $$

   思路是首尾配对然后利用均值不等式。$n$ 为奇数的时候，如法炮制，注意中间多出来了一项：

   $$
   \begin{aligned}
   \frac{2^nn!}{n^n}&=\frac{2}{n}\cdot \frac{4\times 2n}{n^{2}}\cdot \frac{6\times (2n-2)}{n^{2}}\cdots \frac{n+2}{n} \\
   &\le \frac{2(n+2)}{n^{2}}\left( 1+\frac{2}{n} \right)^{\frac{n}{2}\cdot \frac{2(n-2)}{n}}\\
   &\to \frac{2(n+2)}{n^{2}} e^{\frac{2(n-2)}{n}}\to 0\cdot e^2=0
   \end{aligned}
   $$

   还有个想法是利用相邻两项之商：

   $$
   \frac{\frac{2^{n+1}(n+1)!}{(n+1)^{n+1}}}{\frac{2^nn!}{n^n}} =2(n+1)\frac{n^n}{(n+1)^{n+1}}=\frac{2}{\left( 1+\frac{1}{n} \right) ^n}\to \frac{2}{e} < 1
   $$

   当 $n$ 充分大的时候，比如说大于 $M$，比值足够靠近 $1$，可以说明后面的项之积收敛到 $0$，而前 $M$ 项之积为确定的数，所以整体收敛到 $0$。

   还有一种思路是利用 Stirling's Approximation 对 $n!$ 作近似 $\displaystyle \lim_{n \to \infty}\frac{e^nn!}{n^n\sqrt{n}}=\sqrt{2}\pi$，这里不作展开，考试应该也不给用。

   事实上，可以粗浅理解为有限个低阶无穷大之积仍为低阶无穷大。

   （4）等价无穷小方法。先有理化：$\displaystyle \lim_{x \to 0^+} \frac{1-\sqrt{ \cos x}}{x(1-\cos \sqrt{ x})}=\lim_{x \to 0^+}\frac{1-\cos x}{x(1-\cos \sqrt{ x})(1 + \sqrt{\cos x})}$，然后由 $1+\sqrt{\cos x}\to 2$，然后剩下两个用 $\cos x \sim \displaystyle 1 - \frac{x^{2}}{2}$ 有原式 $\displaystyle =\lim_{x \to 0^+}\frac{\frac{x^{2}}{2}}{x\cdot \frac{x}{2}\cdot 2}=\frac{1}{2}$。

   (5) 等价无穷小。$\displaystyle \frac{2 \sin x - \sin 2x}{x^3} = \frac{2\sin x(1 - \cos x)}{x^3}$，然后由 $\sin x \sim  x$ 和 $1 - \cos x \sim \displaystyle  \frac{x^{2}}{2}$，然后就算出答案是 $1$.

   (6) 等价无穷小。$\displaystyle \lim_{n \to \infty}\left[ (n+1)^a - n^a \right] = \lim_{n \to \infty} n^\alpha \left[ \left( 1+\frac{1}{n} \right) ^{\alpha} - 1 \right] = \lim_{n \to \infty}n^\alpha\cdot \alpha \cdot \frac{1}{n}=\alpha \lim_{n \to \infty}n^{\alpha - 1} = \alpha \cdot 0 = 0$。

1. (1) 反证法
   (2) 显然不，考虑调和级数。

1. 首先显然 $x_n>0$。$x_n = \displaystyle \frac{F_{n-1}+F_{n-2}}{F_{n-1}}=1+\frac{1}{x_{n-1}}$，而 $x_1=1$，$x_2=2$，故有 $x_n\in[1,2]$。而 $\displaystyle x_{n+3} - x_{n+1} = \frac{1}{x_{n+2}}-\frac{1}{x_n} = \frac{x_n - x_{n+2}}{x_nx_{n+2}}$，由于 $x_3 = \frac{3}{2}$，$x_4 = \frac{5}{3}$，故有 $x_3 - x_1 > 0$，$x_4 - x_2 < 0$，即奇数子列单调增，偶数子列单调减，单调有界故极限存在。考虑 $\displaystyle x_{n+2} = 1 + \frac{1}{x_{n-1}} = 1 + \frac{1}{1 + \frac{1}{x_{n-2}}}$，两边同时取极限得 $x_n \to \displaystyle \frac{1+\sqrt{5}}{2}$。

1. 取 $m = \max\left\{ e, x \right\}$，则 $\displaystyle \ln m = \frac{\ln m^n}{n}\le \frac{\ln(e^n + x^n)}{n}\le \frac{\ln(2m^n)}{n}\le \ln m + \frac{2}{n}\to \ln m (n\to \infty)$。所以 $f(x) = \displaystyle \begin{cases} 1, & 0\le x\le e \\ \ln x, & x>e \end{cases}$，在 $x=e$ 处连续性显然，所以显然连续。

1. 扩展 Riemann 函数的定义域至 $\mathbb{R}$，然后 $f(x)=\displaystyle \begin{cases} \frac{1}{R(x)} & R(x)\ne 0 \\ 0 & R(x)=0 \end{cases}$。因为任意邻域内都有 $\frac{p}{q}$ 满足 $q$ 很大，而且可以任意大。

1. 这种题肯定是考虑介值定理。而乱取点的话就考虑使用极值点。我们取 $c,d\in[a,b]$ 使得 $\forall x\in[a,b]$ 有 $f(c)\le f(x)\le f(d)$，再令 $\lambda = \displaystyle \sum_{i=1}^n t_if(x_i)$，显然有 $\displaystyle \sum_{i=1}^n t_if(x_i)\ge \sum_{i=1}^n t_if(c) = f(c)$，同理 $\lambda \le f(d)$。所以由介值定理，一定存在一个介于 $c$ 和 $d$ 之间的 $\xi$ 使得 $f(c)\le f(/xi)\le f(d)$。

1. 我们知道 $f(112) = 1$，则显然 $f(\sqrt[3]{112})= 1$，$f(\sqrt[3]{\sqrt[3]{112}})=1$，即我们构造序列 $x_n = 112^{3^{-n}}$，显然 $\forall n$ 有 $f(x_n) = 1$，即 $\displaystyle \lim_{n \to \infty}f(x_n) = 1$，而 $x_n\to 1$，由于 $f(x)$ 为连续函数，所以根据归结原理，我们有 $f(1) =\displaystyle  \lim_{x \to 1}f(x) = \lim_{n \to \infty}f(x_n) = 1$。

   所以，对于 $1031$ 如法炮制，令 $y_n = 1031^{3^{-n}}$，根据上面的理论，$f(y_1) = f(y_2) = \cdots$，而且 $\displaystyle \lim_{n \to \infty}f(y_n) = \lim_{x \to 1}f(x) = f(1) = 1$，所以 $f(1031) = 1$。

1. $n=1$ 略。对于 $n\ge 2$，构造 $g(x) = f(x) - \displaystyle f\left( x + \frac{1}{n} \right)$ 是显然的，然后我们有 $\displaystyle \sum_{k=1}^n g\left( \frac{k - 1}{n} \right) =0$。于是 $\exists$ 一个 $[1, n - 1]$ 间的整数 $m$ 使得 $\displaystyle g\left( \frac{m-1}{n} \right)g\left( \frac{m}{n} \right) \le 0$（证明的话可以考虑反证），由介值定理知 $\exists \xi \in \displaystyle \left[ \frac{m-1}{n},\frac{m}{n} \right]$ 满足 $g(\xi) = 0$。

1. 这个叫做压缩映射，前面三个小问都是为了推出第四问做铺垫的。分别考虑吧。
   (1) 证明：考虑对于一个特定值 $x_0$，我们有 $0\le|f(x) - f(x_0)|\le k|x-x_0|\le |x-x_0|$，令 $x\to x_0$，夹逼得到 $|f(x)-f(x_0)|\to 0$，这与 $\displaystyle \lim_{x \to x_0}f(x) = f(x_0)$ 等价，所以其连续。
   (2) 不妨令 $x<y$，展开绝对值，有：$k(x-y)\le f(x) - f(y)\le k(y-x)$，即 $kx-f(x) \le ky - f(y)$，故 $kx - f(x)$ 单调增。
   (3) 为了找到不动点，研究 $g(x) = x - f(x)$，有 $g(x) = (1-k)x + kx - f(x)$，其显然为单调增函数，并且 $\forall x<y$ 有 $g(y) - g(x)>(1-k)(y-x)$。取一 $p\in[a,b]$，若 $g(p)=0$，证毕。若 $g(p)\ne 0$，WLOG 取 $g(p)<0$，利用 $g(y) - g(x)>(1-k)(y-x)$ 的结论，取 $q = p + \displaystyle \frac{-2g(p)}{1-k} > p$，故 $\displaystyle g(q) > g(p) + (1-k)\cdot \frac{-2g(p)}{1-k} = -g(p)<0$，由介值定理立得 $\exists c\in(p,q)$ 使得 $g(c) = 0$，证毕。
   (4) 若 $x_1$ 为不动点，则序列显然收敛，考虑其不为不动点的情况。WLOG，讨论 $x_1<c$ 的情况。这个时候由 $g(x)$ 的单调性立得 $g(x_1) < g(c) = 0$，即 $x_1<f(x_1)$，这个时候显然有 $\displaystyle x_2 = \frac{x_1+f(x_1)}{2}>x_1$，如果我们能证明 $x_2<c$ 的话，就可以说明 $\{x_n\}$ 单调增了：展开绝对值有 $x_1 - c\le c - f(x_1)\le c - x_1$，移项有 $2c\ge x_1+f(x_1)$，即 $x_2 < c$。$\{ x_n \}$ 单调增且有上界 $c$，所以其必然收敛。$x_1>c$ 的情况完全对称，证毕。

1. (1) 加一项减一项：$\displaystyle \lim_{\Delta x \to 0}\frac{f(\Delta x) - f(-\Delta x)}{2\Delta x} = \lim_{\Delta x \to 0}\frac{f(\Delta x)-f(0)}{\Delta x - 0} + \lim_{\Delta x \to 0}\frac{f(0) - f(-\Delta x)}{0 - (-\Delta x)} = 2f'(0)$。
    (2) 这一问稍微有意思些，注意到 $f'(0)$ 存在首先 $f(0)$ 要存在，我直接挖掉 $f(0)$ 的话，那一坨极限仍然可以存在但是 $f(0)$ 的导数显然是不存在的。

1. (1) 回归导数的定义。考虑一个 $\varepsilon>0$，根据导数的定义，我们希望当 $n$ 充分大（大于某个 $N_0$）的时候有 $\displaystyle \left\vert \frac{f(x_n)-f(0)}{x_n}-f'(0) \right\vert <\varepsilon$，且 $\displaystyle \left\vert \frac{f(y_n) - f(0)}{y_n} - f'(0) \right\vert  < \varepsilon$，即
   $$
   \left\vert f(x_n) - f(0) - x_nf'(0) \right\vert < \varepsilon|x_n|\\
   \left\vert f(y_n) - f(0 ) - y_nf'(0) \right\vert  < \varepsilon |y_n|
   $$
   两边相加，我们得到
   $$
   \left\vert f(x_n) - f(0) - x_nf'(0) \right\vert + \left\vert f(y_n) - f(0 ) - y_nf'(0) \right\vert < \epsilon (|x_n| + |y_n|)
   $$

   左边用三角形不等式，右边利用 $x_n$ 和 $y_n$ 一正一负：

   $$
   \left\vert f(x_n) - f(0) - x_nf'(0) \right\vert + \left\vert f(y_n) - f(0 ) - y_nf'(0) \right\vert\ge |f(x_n) - f(y_n) - f'(0)(x_n - y_n)|\\
   \varepsilon(|x_n| + |y_n|) = \varepsilon\left\vert x_n - y_n \right\vert 
   $$

   所以
   $$
   |f(x_n) - f(y_n) - f'(0)(x_n - y_n)| < \varepsilon\left\vert x_n - y_n \right\vert
   $$
   也即
   $$
   \left\vert \frac{f(x_n)-f(y_n)}{x_n-y_n} - f'(0) \right\vert < \varepsilon
   $$
   这就是说明
   $$
   \lim_{n \to \infty}\frac{f(x_n)-f(y_n)}{x_n-y_n} = f'(0)
   $$
   (2) 构造。$f(x) = \displaystyle\begin{cases} x^2\sin \frac{1}{x} & x\ne 0 \\ 0&x=0  \end{cases}$，我们有 $\displaystyle f'(0) = \lim_{x \to 0}\frac{x^2\sin \frac{1}{x} - f(0)}{x - 0} = \lim_{x \to 0}x\sin \frac{1}{x} = 0$，下面我们找两个序列让 $\displaystyle \lim_{n \to \infty}\frac{f(x_n)-f(z_n)}{x_n-z_n}$ 不为 $0$ 的。考虑让 $\displaystyle \sin \frac{1}{x_n}$ 为 $1$，$\displaystyle \sin \frac{1}{z_n}$ 为 $-1$，则我们可以给出如下构造：
   $$
   x_n=\frac{1}{2n \pi + \frac{\pi}{2}}\\
   z_n = \frac{1}{2n\pi - \frac{\pi}{2}}
   $$
   来算：
   $$
   \begin{aligned}
   \frac{f(x_n) - f(z_n)}{x_n - z_n} &= \frac{\left( \frac{1}{2n \pi + \frac{\pi}{2}} \right) ^{2} + \left( \frac{1}{2n\pi - \frac{\pi}{2}} \right)^{2} }{\frac{1}{2n \pi + \frac{\pi}{2}} - \frac{1}{2n\pi - \frac{\pi}{2}}}\\
   &= \frac{\frac{8n^2\pi^{2} + \frac{\pi^{2}}{2}}{\left( 2n\pi + \frac{\pi}{2} \right) ^{2}\left( 2n\pi - \frac{\pi}{2} \right) ^{2}}}{\frac{-\pi}{\left( 2n\pi + \frac{\pi}{2} \right) \left( 2n\pi - \frac{\pi}{2} \right) }}\\
   &= -\frac{8n^2\pi + \frac{\pi}{2}}{4n^2\pi^2 - \frac{\pi^{2}}{4}}\to \frac{-2}{\pi}\ne 0
   \end{aligned}
   $$