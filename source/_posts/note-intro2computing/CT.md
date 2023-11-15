---
title: 计算概论 A 课程笔记：范畴论与 Monad 部分
date: 2023-10-22 20:10:43
tags:
  - Haskell
  - 函数式编程
  - 笔记
categories: [笔记, 本科课程]
---

## Preface

因为我觉得我要沉淀下期中了所以自己记给自己看一下。

不保证内容的可读性，因为懒得去想对应语言的表达所以中英文夹杂了一下。

不保证内容的正确性，如果能帮忙指出错误，感激不尽。

## Category

### Definition

一个范畴 $\mathsf C$ 包含着如下东西：

- 一堆对象，记作 $\mathsf C_0$；
- 一堆态射（morphism），记作 $\mathsf C_1$。
- 态射的来源与目标（domain 与 codomain），此后我们使用 $f:a\to b$ 或 $f_{a\to b}$ 表示一个态射，which satisfies $\operatorname{dom} f = a$ 且 $\operatorname{cod} f = b$。
- 单位态射：对每个 $a\in \mathsf C_0$，都有态射 $f_{a\to a}\in\mathsf C_1$，记作 $1_a$。
- 态射的复合：$g_{b\to c}\circ f_{a\to b}$ 到 $h_{a\to c}$。

满足如下两条公理：

1. Unit：$\forall f_{a,b}\in \mathsf C_1, f\circ 1_a = f = 1_b\circ f$
2. Associativity：$\forall f_{a\to b},g_{b\to c},h_{c\to d} \in \mathsf C_1, h\circ (g\circ f) = (h\circ g)\circ f$

**对象可以被理解为某种代数结构，态射可以被理解为一种对应关系。对象可以抽象成点，态射可以抽象成有向边。**

而且 co 这个前缀似乎挺有意思的。冷笑话：读 CT 的书的每个人都是其 co-author。

### Some Examples of Category

#### Some Trivial Ones

- 零范畴。顾名思义，啥都没有。
- 范畴 $\mathsf{One}$。其中 $\mathsf{One}_0 \dot= \{*\}$，$\mathsf{One}_1\dot={1_*}$。
- 范畴 $\mathsf{Two}$。其中 $\mathsf{Two}_0 \dot= \{*,\star\}$，$\mathsf{Two}_1 \dot= \{1_*,1_{\star},f_{*\to\star}\}$

给出有限范畴的定义：$\mathsf C$ 为有限范畴 iff $\mathsf{C}_1$ 为有限集合。*注意到 $\mathsf C_0$ 必然是有限集合*。

#### Fn

- $\mathsf {Fn}_0\dot=\{a\mid a\text{ is a set}\}$
- $\mathsf {Fn}_1\dot= \{f\mid f\text{ is a function between two sets in }\mathsf {Fn}_0\}$，即我们这里可以把 $\mathsf{Fn}$ 中的态射理解为两个集合之间的函数关系
- $\operatorname{dom} f$ 和 $\operatorname{cod}f$ 定义为 $f$ 的定义域与值域
- $1_a\dot= \{x\mapsto x\mid x\in a\}$
- 态射的复合被定义为函数的复合

Unit 和 Associativity 都是显然的。

### Some Extra Definitions

- Isomorphism（同构态射）：$f_{x\to y}$ 为同构态射 $\iff$ $\exists g_{y\to x}$ s.t. $g\circ f = 1_x\land f\circ g = 1_y$，$g$ 可以记作 $f^{-1}$。
- 一个范畴中的两个对象 $x,y$ 被定义为 isomorphic，记作 $x\cong y$，当且仅当存在 $x$ 与 $y$ 之间的同构态射。
- endomorphism（自同态态射）：$\operatorname{dom} f = \operatorname{cod} f = a$。
- automorphism（自同构态射）：既是 isomorphism 也是 endomorphism。

It's noteworthy that: 

- isomorphism 总是成对出现的（不解释了），且 $f^{-1}$ 是唯一的。
- “两个对象是否同构”这件事情要放在同一个范畴下讨论。
- 对于任意范畴中的任意对象 $a$，$1_a$ 都是 automorphism。因为 $1_a\circ 1_a=1_a=1_a\circ 1_a$ 且 $1^{-1}_a=1_a$。

### Groupoid, Group and Monoid

关于这三者的等价定义（在 CT 语境内给出）：

> - 一个范畴 $\mathsf C$ 是一个 groupoid $\iff$ $\forall f\in \mathsf C_1$ 都有 $f$ 为 isomorphism。
>
> - 一个范畴 $\mathsf C$ 是一个 group $\iff$ $\mathsf C$ 为一个 groupoid 且 $|\mathsf C_0| = 1$。
>
>   或者说，仅具有一个对象，且所有态射都是同构态射。
>
> - 一个范畴 $\mathsf C$ 是一个 monoid $\iff |\mathsf C_0|=1$。

~~此处有历史遗留问题待解决。~~已解决，感谢 ZJG。

举个例子，对于幺半群 monoid，其传统定义如下。

一个幺半群是一个三元组 $(m,u,\oplus)$，其中

1. $m$ 是一个集合
2. $u$ 是 $m$ 中唯一的一个单位元
3. $\oplus$ 是一个二元运算符 $m\times m\to m$

满足

1. $\forall x\in m$ 有 $u\oplus x = x = x\oplus u$
2. 结合律：$x\oplus (y\oplus z) = (x\oplus y)\oplus z$

而我们如果用 CT 的语言去表述，则一个幺半群 $(m,u,\oplus)$ 是一个范畴 $\mathsf M$，其中

1. $\mathsf M_0 \dot= \{m\}$
2. $\mathsf M_1 = m$（解释见下）
3. $\forall f\in \mathsf M_1$ 都有 $\operatorname{dom} f = \operatorname{cod} f = m$
4. $1_m\dot=u_{m\to m}$
5. $(g_{m\to m}\circ f_{m\to m})_{m\to m} \dot=(g\oplus f)_{m\to m}$

满足如下两条公理

1. $f\circ 1 =f= 1\circ f$
2. $h\circ (g\circ f) = (h\circ g)\circ f$

其实，原理就是，$m$ 这个集合作为范畴中唯一的对象，而将 $m$ 中所有的元素都定义为范畴中的态射，这个态射不必要理解为某种函数或者是什么对应关系，可以只当作形式化记号。而对态射复合 $(g_{m\to m}\circ f_{m\to m})_{m\to m} \dot=(g\oplus f)_{m\to m}$ 的定义使得整件事情良定义了起来。

感觉还是很美丽的啊，非常的良定义！

## Functor

### Definition

一个定义在两个范畴 $\mathsf C,\mathsf D$ 上的 functor $F:\mathsf C\to\mathsf D$ 包含以下两种操作：

- 对象之间的映射。

  即将 $\mathsf C_0$ 中的每个对象映射到 $\mathsf D_0$ 中，可以记作 $F~a$。

- 态射之间的映射。

  即将 $\mathsf C_1$ 中的每个态射 $f_{a\to a'}$ 映射到 $\mathsf D_1$ 中的 $g_{F~a\to F~a'}$ 上，可以记作 $F~f$。

需要满足两条公理：

1. $\forall a\in \mathsf C_0, F~1_a = 1_{F~a}$
2. $\forall f_{a\to b},g_{b\to c}\in \mathsf C_1, F~g\circ F~f = F~(g\circ f)$。

并且，一个满足 $\mathsf C = \mathsf D$ 的 functor $F_{\mathsf C\to\mathsf D}$ 被称为 endofunctor（自函子）

### Examples

- $1_C$

  很蠢的“单位函子”，不说了。两条公理是自明的。

- $P_{\mathsf{Fn}\to\mathsf{Fn}}$

  $P~a~\dot=~\{x\mid x\subseteq a\}$

  $P~f_{a\to b}~\dot=~\{x\mapsto \{f ~i \mid i\in x\}\mid x\in P~a\}$

  类似于幂集的感觉。

  两条公理的证明不是重点~~而且我也不太会~~略过。

### What about in Haskell

```haskell
class Functor f where
  fmap :: (a -> b) -> (f a -> f b)
-- which satisfies
-- Identity:    fmap id == id
-- Composition: fmap (f.g) == fmap f . fmap g
```

可以发现，`f` 不就是 $\mathsf {Fn}_0\to \mathsf {Fn}_0$ 的一个 object-mapping 吗，`fmap` 不就是 $\mathsf{Fn}_1\to \mathsf{Fn}_1$ 的一个 morphism-mapping 吗。这两个东西组合起来，就是一个很良定义的 functor。

> 补充：这两个 law 实际上就是保证了“范畴的结构不会被改变”这件事情。
>
> 而且可以证明，For any parameterized type in Haskell, there is at most one function fmap that satisfies the required laws. 具体怎么证明我也不知道。

因为 Haskell 中我们都是研究“函数”，所以我们研究的范畴为 $\mathsf{Fn}$。而且我们发现，$F$ 是一个 endofunctor。

下面举几个例子，例如 Maybe：

```haskell
data Maybe a = Nothing | Just a

instance Functor Maybe where
  fmap _ Nothing = Nothing
  fmap g (Just x) = Just (g x)
```

再比如说树

```haskell
data Tree a = Leaf a | Node (Tree a) (Tree a)
			deriving Show

instance Functor Tree where
  fmap g (Leaf x) = Leaf (g x)
  fmap g (Node l r) = Node (fmap g l) (fmap g r)
```

是不是感觉具体了一些，此时我们可以把 functor 大概理解为一个“结构”或者说“壳子”。将抽象层级再次提高了。例如说所谓的 `inc` 函数，利用 functor 我们可以定义为

```haskell
inc :: Functor f => f Int -> f Int
inc = fmap (+1)
```

注意到，刚才提到的 functor 的两条 law 保证了整个的 structure 没有改变，例如我们对于 functor `[]`，用 `fmap` 操作之后，元素个数不会增加/减少，顺序不会改变，都是一一对应的。

### Natural Transformation

坑着。

## Applicative

> 后记：为啥要叫 applicative，大抵是因为他做到了更广泛的 function application 的映射罢。。。。

### Introduction

In retrospect, we know functors **abstracts the idea of mapping a function over each element of a structure**。

现在若我们想解决含有更多参数的函数，怎么办呢？

```haskell
fmap0 :: a -> f a
fmap1 :: (a -> b) -> f a -> f b
fmap2 :: (a -> b -> c) -> f a -> f b -> f c
fmap3 :: (a -> b -> c -> d) -> f a -> f b -> f c -> f d
```

如果我们能利用 curried function 的想法，将如上的东西也抽象，定义一个更加 generalized 的 `fmap`，岂不是很好？

这样子考虑，引入两个函数：

```haskell
pure :: a -> f a
(<*>) :: f (a -> b) -> f a -> f b
```

注意。 `<*>` 运算符是具有左结合性的，正如我们的 function application 一样。他能帮我们做到什么事情呢？注意看下面的代码：

```haskell
fmap0 :: a -> f a
fmap0 = pure

fmap1 :: (a -> b) -> f a -> f b
fmap1 g x = pure g <*> x
-- fmap1 g x = g <$> x

fmap2 :: (a -> b -> c) -> f a -> f b -> f c
fmap2 g x y = pure g <*> x <*> y
-- fmap2 g x y = g <$> x <*> y
-- 类型解读：
-- g :: (a -> b -> c)
-- x :: f a
-- y :: f b
-- pure g <*> x :: f (a -> (b -> c)) -> f a -> f (b -> c)
-- 注：一开始没有想清楚是因为没有把 (b -> c) 当成一个整体看待
-- 所以我们拿到了这个 f (b -> c) 的类型后，继续往右结合。
-- (pure g <*> x) <*> y :: f (b -> c) -> f b -> f c
-- 整件事情是不是就变得合理了起来

fmap3 :: (a -> b -> c -> d) -> f a -> f b -> f c -> f d
fmap3 g x y z = pure g <*> x <*> y <*> z
-- fmap3 g x y z = g <$> x <*> y <*> z
```

注：其中 `<$>` 运算符被定义为

```haskell
(<$>) :: Functor f => (a -> b) -> f a -> f b
-- It's an infix synonym for fmap
-- 作为对比，让我们看看之前学过的 function application operator：
($) ::                (a -> b) -> a -> b
```

于是我们可以给出 applicative 的定义：

```haskell
class Functor f => Applicative f where
  -- lift a value
  pure :: a -> f a
  -- sequential application
  (<*>) :: f (a -> b) -> f a -> f b
```

### Examples

Let's slow down for a while to see how we can make some known types into applicative functors.

```haskell
instance Applicative Maybe where
  pure = Just
  
  Nothing <*> _ = Nothing
  (Just g) <*> mx = fmap g mx
```

```haskell
instance Applicative [] where
  pure x = [x]
-- (<*>) :: [a -> b] -> [a] -> [b] 
  gs <*> xs = [g x | g <- gs, x <- xs]
```

这个可以理解为 `pure` transforms a value into a singleton list, while `<*>` takes a list of functions and a list of
arguments, and applies each function to each argument in turn, returning all the results in a list. （我直接抄的书懒得管那么多了）

```bash
> pure (*) <*> [1,2] <*> [3,4]
[3,4,6,8]
```

看这一段代码吧，实际上，`pure (*) <*> [1,2]` 即为 `[(1*), (2*)]`，然后再和 `[3,4]` 复合的话就是（前面的是外层循环，后面的是内层循环）。

利用 list 的 applicative 性，我们可以改写一些函数来避免中间变量的书写，例如

```haskell
prod :: [Int] -> [Int] -> [Int]
-- prod xs ys = [x * y | x <- xs, y <- ys]
prod xs ys = (*) <$> xs <*> ys
```

最后来看一下之前遇到过的 `IO` 类型。

```haskell
instance Applicative IO where
  pure = return
  
  mg <*> mx = do {g <- mg; x <- mx; return (g x)}
```

可能有点抽象，看个例子先吧。

```haskell
getChars :: Int -> IO String
getChars 0 = return []
getChars n = pure (:) <*> getChar <*> getChars (n - 1)
```

### Applicative 与副作用的思考

一开始我们定义 applicative 实际上是想要达到某种“将多参数的函数进行映射”的想法。

但是其实我们可以将其视作一种，programming with effects 的 approach（我不想翻译了，对不起）

什么意思呢？有没有注意到，被 applicative functor 包裹起来的这个整体，是不是或多或少带点副作用而不是单纯的 plain value 了。例如 `Maybe` 就是说，有可能失败；`IO` 则是可能涉及到 IO 操作。

所以说，applicative 在做的事情，也可以理解为 applying pure functions to effectful arguments，具体是什么 effect 取决于 functor。

标准库里面定义了如下的一个 generic function：

```haskell
sequenceA :: Applicative f => [f a] -> f [a]
sequenceA [] = pure []
sequenceA (x:xs) = pure (:) <*> x <*> sequenceA xs
```

就是说，将一系列的 applicative 操作变成了一个，同时返回一个返回值的列表。比如说我们刚才的 `getChars` 就可以被定为为

```haskell
getChars :: Int -> IO String
getChars = sequenceA [replicate n getChar]
```

是不是，重复 $n$ 次读入字符这个操作，然后把结果合并，很合理吧。

### Applicative Laws

```haskell
pure id <*> x = x
pure (g x) = pure g <*> pure x
x <*> pure y = pure (\g -> g y) <*> x
x <*> (y <*> z) = (pure (.) <*> x <*> y) <*> z
```

分别注解一下。

第一行比较显然，不解释。分析类型：`id :: a -> a`，`x :: f a`

第二行说的就是，function application 在 pure 之后仍得到保留，分析类型：`pure (g x) :: f b`，`g :: a -> b`

（存疑）第三行比较有趣。说的是如果我们将一个 effectful function 应用到一个 pure 的参数上时，先处理哪部分是无关紧要的。请注意这里 `x` 的类型是 `f (a -> b)`，`y` 的类型是 `a`，`g` 是一个 lambda function，其类型为 `a -> b`，所以在 pure 了之后，`pure (\g -> g y)` 的类型为 `f ((a -> b) -> b)`，后面再接一个 `x :: f (a -> b)` 其实就相当于是把 `x` 作为参数。

第四行，分析类型吧。`z :: f a`，`y :: f (a -> b)`，`x :: f (b -> c)`，`(pure (.) <*> x <*> y) :: f (a -> c)`。

~~至于为什么要有这些 law，存疑。等周三上课吧。~~老师也不太会，没事了。

好他妈难啊。

## Monad

### Kleisli Category

背景是实现所谓的 logging，即对于一个函数，我们让他需要同时打印一段 log。

于是我们定义了下面的这个东西：

```haskell
type Writer a = (a, String)

(>=>) :: (a -> Writer b) -> (b -> Writer c) -> (a -> Writer c)
m1 >=> m2 = \x -> let (y, s1) = m1 x
                      (z, s2) = m2 y
                  in (z, s1 ++ s2)

return :: a -> Writer a
return x = (x, "")
```

我们是从范畴 $\mathsf{Fn}$ 出发构建我们的 $\mathsf{Writer}$ 范畴的。对象保持不变，但是态射从原来的 function 变成现在的 **embellished function**。

> embellished function 其实可以理解为，带副作用的函数。比如说这里要实现的功能就是在正常计算值的基础上，完成 logging 的过程。

这个时候我们还是将 embellished function 理解为 `a -> b` 的态射，虽然他实际上是一个 `a -> m b` 的函数。不过我们是可以良定义出态射的复合的，也就是上面的 `>=>` 符号。

同时，单位态射也得到了定义，上面的 `return`。

再次重申，这篇文章是给我自己看的。

上面的 $\mathsf{Writer}$ 范畴就是 Kleisli Category —— 基于 Monad 的范畴的一个例子。这里还是先直接给出形式化定义吧。

给定一个范畴 $\mathsf C$ 和一个自函子 $m: \mathsf C\to \mathsf C$，则其对应的 Kleisli 范畴 $\mathsf K$ 是如下定义的：

- $\mathsf K_0~\dot=~\mathsf C_0$

- $\mathsf K_1 ~\dot=~ \{f_{a\to b}\mid f_{a\to b}\text{ is a morphism }f'_{a\to m~b}\text{ in }\mathsf C_1\}$

  这句话可能有些不好理解，等一下会说。

- $\operatorname{dom}$ 和 $\operatorname{sub}$ 的定义略。

- $1_a ~\dot=~ \mathsf{return}_{a\to m~a}$，其中 $\mathsf{return}_{a\to m~a}\in\mathsf C_1$

- $(g_{b\to c}\circ f_{a\to b})_{a\to c}~\dot=~f'\rightarrowtail g'$，其中 $\rightarrowtail$ 是一个复合操作，其对于 $\mathsf C_1$ 中的态射 $f'_{a\to m~b},g'_{b\to m~c}$ 给出了一个其复合后的态射 $h'_{a\to m~c}\in\mathsf C_1$。

满足如下两条公理：

1. Unit：$f\circ 1=f=1\circ f$
2. Associativity：$h\circ (g\circ f) = (h\circ g)\circ f$。

> 之前没有理解的地方是 $\mathsf K_1$ 的定义。实际上呢，可以理解为，我们对于 $\mathsf C_1$ 中的态射 $f'_{a\to m~b}$，将其就视为是 $\mathsf K_1$ 中 $a\to b$ 的态射。他实际上做的事情是 $a\to m~b$，但是在 $\mathsf K$ 中就把他视为 $a\to b$ 的态射。
>
> 然后定义了那个小鱼符号之后，态射的复合就也是良定义的了。
>
> 不知道这么说够不够清楚。

然后，$m$ 这个 functor 就是一个 monad。

> All told, a monad in X is just a monoid in the category of endofunctors of X, with product × replaced by composition of endofunctors and unit set by the identity endofunctor.

### In Haskell

我们其实没有定义小鱼符号。因为其也不是那么的简洁，instead，我们定义 bind。

```haskell
(>>=) :: m a -> (a -> m b) -> m b
```



