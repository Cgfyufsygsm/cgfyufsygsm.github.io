---
title: 计算概论 A 课程笔记：Agda
date: 2023-11-15 17:00:01
tags:
  - Agda
  - 函数式编程
  - 笔记
categories: [笔记, 本科课程]
---

是写给自己看的备忘录，如果有写的不清楚的东西见谅（

真的很感谢助教大人，他们帮到了我很多。

## Intro to Calculational Programming

- Specification

  可以理解为“目的”一类的东西。

  - describes **what** task an algorithm is to perform,
  - expresses the programmers' intent,
  - should be as clear as possible.
  
- Implementation
  
  字面意思，实现。
  
  - describes **how** task is to perform,
  - expresses an algorithm (an execution),
  - should be effciently done within the time and space available.

The link is that the implementation should be **proved** to satisfy the specification.

Specification 可以用 predicates（谓词）表达，亦可以用 functions 表达。

主要将目光聚焦于 functional specification, since

- It's **executable**
- It's powerful to express intended mappings directly by functions or through their *composition*
- It's suitable for **reasoning**（推理）, when functions used are *well-structured* with good algebraic properties（性质）

----

注意到，对于最大子段和问题，$O(n)$ 的贪心做法和 $O(n^3)$ 的朴素做法是有很大的区别的。

如果对于 more general case 我们能够有通用的 calculation 从朴素做法得到一个高效率做法，这就是 program calculation 做的事情。不过好像 It's still under research. Whatever.

不是很重要，重要的是 Agda 好他妈难啊。

> Program testing can be used to show the presence of bugs, but never to show their absence!
>
> Edsger Dijkstra

现在的核心思想是，we can verify software correctness by writing mathematical proofs about it.

Agda 是一种函数式语言 which is implemented in Haskell。他除了要求 pure functional 外还要求 always terminate.

- **Types: Propositions**：命题是通过类型的方式书写的。
- **Terms (functional programs): Proofs**：写出来的这段程序就是证明。

## Agda and Booleans

基本操作：

- Load/加载文件：`C-c C-l`，其中 `C-a` 表示 `Ctrl+A`；
- Compile/编译：`C-x C-c`；
- Compute/Normalize/计算：`C-c C-n`。
- Type inference/类型推断：`C-c C-d`

下面来看 `bool` 标准库。

```agda
module bool where

open import level

--------------------------------------------------------
-- datatypes
--------------------------------------------------------

data 𝔹 : Set where
  tt : 𝔹
  ff : 𝔹
```

`module`：每个 Agda 文件都要包含一个模块声明，而且和文件名必须一样。

注释的规则与 Haskell 几乎是一样的。

`𝔹` 是一个 type constructor，里面包含了两个 data constructor：一个 `tt` 一个 `ff`。

同时，`𝔹` 的类型是 `Set`。Agda 用了一个很巧妙的方法来避免罗素悖论：给 `Set` 赋予层级，其中 `Set` 就是 `Set 0` 的简写。

```agda
~_ : 𝔹 → 𝔹
~ tt = ff 
~ ff = tt

_&&_ : 𝔹 → 𝔹 → 𝔹
tt && b = b
ff && b = ff

_||_ : 𝔹 → 𝔹 → 𝔹
tt || b = tt
ff || b = b
```

函数的声明和 Haskell 的感觉很像，`~` 后面加下划线表示接收参数。Pattern matching 的思路和 Haskell 的也是几乎一模一样的。但是**注意一定要加空格**，写成 `~tt` 是会 parse error 的，Agda 会把 `~tt` 整体当成一个标识符。

二元运算符被定义为 curried function 也是和 Haskell 类似，此处略过。

注意 Agda 对缩进同样敏感。

```agda
if_then_else_ : ∀ {ℓ} {A : Set ℓ} 
                  → 𝔹 → A → A → A
if tt then y else z = y
if ff then y else z = z
```

调用的时候还可以写 `if_then_else_ x y z` 的。此处可以按下 `C-c C-n`，然后输入 `if tt then tt else ff`，Agda 会给你返回 `tt`。`∀` 通过 `\all` 输入，`ℓ` 通过 `\ell` 输入。

## Intro to Constructive Proof

### 初探 equality type 证明

教材上的 introduction，我觉得相当令人豁然开朗。

> Admittedly, we'd have to be having a pretty bad coding day to define `~ ff` to be `ff`, but it could happen. And certainly if we were writing some more complicated code, we know very well that we could make mistakes and accidentally introduce bugs into the code without meaning to. If we wrote the previous definition, evaluating `~ ~ tt` would give us `ff`, instead of the expected `tt`. So it would be nice to record and check the fact that `~ ~ tt` is supposed to be `tt`. We could write some test code and remember to run it from time to time to check that we get the expected answer. But we can use this test case as a very rudimentary form of theorem, and use Agda's theorem-proving capabilities to confirm that the theorem holds (i. e., the test succeeds).

以我们要证明 `~ ~ tt` 与 `tt` 相等为例，在 Agda 中，通过 **equality type** 实现：`~ ~ tt ≡ tt`。这样的“等式”被称作 **propositional equality**。（`≡` 通过 `\equiv` 或 `\==` 打出来）Agda 是把形如这样的 formula 当作类型来判断的。逻辑命题可以被视作类型是类型论中非常重要的思想，即 **Curry-Howard isomorphism**。

```agda
~~tt : ~ ~ tt ≡ tt
~~tt = refl

~~ff : ~ ~ ff ≡ ff
~~ff = refl

~~-elim : ∀ (b : 𝔹) → ~ ~ b ≡ b
~~-elim tt = ~~tt
~~-elim ff = ~~ff
```

Agda 可以将 `~ ~ tt` 按照 `~_`  的定义展开，然后我们相当于要证明 `tt ≡ tt`，这可以通过 **reflexivity** 实现。其中 equality 的定义如下：`_≡_` 是一个 type constructor, which has only one data constructor: refl。

```agda
data _≡_ {ℓ} {A : Set ℓ} (x : A) : A → Set ℓ where
  refl : x ≡ x
```

对于 `~~-elim`，就是更一般化的情形，我们可以将其视作接收一个 `𝔹` 类型的值并返回一个证明。全称量词其实没有什么屁用，写着好看而已，去掉不会影响任何事情。

> 我们再去理解整件事情的话，对于一个 type，其可以表示一个 proposition，如果这个 type 有良定义的 value 存在，说明有证明。就比如说 `~~tt`，它的 type 就是 `~ ~ tt ≡ tt`，而我们通过定义把 LHS 展开变成 `tt ≡ tt`，所以是可以用 `refl` 这个 data constructor 构造一个良定义的 value，其即得到了证明。只要其能通过 Agda 的类型检查，则你写的这个证明就是没有问题的。
>
> Agda 还要求程序一定要能 terminate，这是避免类似于循环论证的情况出现。如果写出 `a = a` 这样的东西，那肯定是不合逻辑的。 
>
> 而，命题的合取可以用 pair 表示：`(p1, p2)` 是个 type，其有良定义的 value 存在当且仅当 `p1` 和 `p2` 都为真。
>
> 命题的析取通过类似 Haskell 中 or 的感觉实现，比如 `data Maybe a = Nothing | Just a` 的感觉，只要一个良定义，整个就可以良定义。
>
> $p_1\implies p_2$ 可以通过“函数”实现。比如说 `p1 -> p2`。

### Implicit Argument 以及含假设的定理证明

用花括号括起来的参数叫做 **implicit argument**，比如说下面这个例子：

```agda
&&-idem : ∀ {b} → b && b ≡ b
&&-idem{tt} = refl
&&-idem{ff} = refl
```

我们采用 pattern matching 的方式去写。但是为何我们不能这么写呢？

```agda
&&-idem : ∀ {b} → b && b ≡ b
&&-idem = refl
```

实际上是因为，对于一个抽象的 `b`，我们没法直接按照定义去展开 `b && b`。

隐式参数的好处在于可以使程序更为简洁，比如说我们可以这样：

```agda
sb : tt && tt ≡ tt
sb = &&-idem
```

Agda 会自动推断传进 `&&-idem` 的参数，这使得代码更为简洁。

再看下一个例子。

```agda
||≡ff₁ : ∀ {b1}{b2} → b1 || b2 ≡ ff → b1 ≡ ff
||≡ff₁ {ff} p = refl
||≡ff₁ {tt} ()
```

注意到我们的隐式参数是两个 `𝔹`，然后接收一个证明 whose type is `b1 || b2 ≡ ff`，返回一个证明 whose type is `b1 ≡ ff`。这其实就是所谓的带有假设的定理证明。至于我们的分类讨论，其实也就是在做 pattern matching。注意一下第三行的 `()` 记号：`absurd pattern`，这是在说明这个假设不可能成立。

我们还可以这么写：

```agda
||≡ff₁ : ∀ {b1 b2} → b1 || b2 ≡ ff → b1 ≡ ff
||≡ff₁ {ff} p = refl
||≡ff₁ {tt} p = p
```

这是因为 `tt || b2 ≡ ff` 可以用 `_||_` 的定义展开成 `tt ≡ ff`，和最后要证明的东西的 type 对应得上（即 `tt ≡ ff`），但是如果浅浅修改一下就不可以了：

```agda
||≡ff₂ : ∀ {b1 b2} → b1 || b2 ≡ ff → ff ≡ b1
||≡ff₂ {ff} p = refl
||≡ff₂ {tt} p = p
```

如果这么写的话，Agda 会报错：

```bash
Error:
tt != ff of type 𝔹
when checking that the expression p has type ff ≡ tt
```

为什么？因为 `p` 的 type 经过展开后是 `tt ≡ ff`，和我们需要的 `ff ≡ tt` 是不一样的，这就很错。所以说**似乎用 absurd pattern 会更好一些**。

### Matching on Equality Proofs 以及 rewrite

```agda
||-cong₁ : ∀ {b1 b1' b2} → b1 ≡ b1' → b1 || b2 ≡ b1' || b2
||-cong₁ refl = refl

||-cong₂ : ∀ {b1 b2 b2'} → b2 ≡ b2' → b1 || b2 ≡ b1 || b2'
||-cong₂ p rewrite p = refl
```

看第一个，关键点在于第一个参数是 `refl` 的话，那么之后 Agda 就知道了可以做同义替换，目标变成 `b1 || b2 ≡ b1 || b2`，自然可以用 `refl` 证明。

dot pattern 什么的内容略过了，Agda 2.6 更新后无所谓了已经。

接下来是一个很重要的东西：`rewrite`。

看第二个定理，如果我们这样写：

```agda
||-cong₂ : ∀ {b1 b2 b2'} → b2 ≡ b2' → b1 || b2 ≡ b1 || b2'
||-cong₂ = ?
```

再按下 `C-c C-l`，他会引入一个 hole：

```agda
||-cong₂ : ∀ {b1 b2 b2'} → b2 ≡ b2' → b1 || b2 ≡ b1 || b2'
||-cong₂ p = {!   0!}
```

*把鼠标光标放在 goal 内*，按下 `C-c C-,`，他会把 context 和 goal 列出来：

```agda
Goal and Context

(b3 || b4) ≡ (b3 || b2')

p : b4 ≡ b2'
b2' : 𝔹   (not in scope)
b4 : 𝔹   (not in scope)
b3 : 𝔹   (not in scope)
```

现在目标变成了证明 `(b3 || b4) ≡ (b3 || b2')`（这里变量换了个名字是因为没有把 implicit argument 写出来所以 Agda 随便取了一个），如果你引入 `rewrite p` 记号，这意味着你**可以用 `p` 的内容去 rewrite goal 中的内容**，在这里就是把 `b2'` 替换成 `b4`：

```agda
||-cong₂ : ∀ {b1 b2 b2'} → b2 ≡ b2' → b1 || b2 ≡ b1 || b2'
||-cong₂ p rewrite p = {!   0!}
```

列出 goal，发现就变成了 `(b3 || lhs) ≡ (b3 || lhs)`，这可以用 refl 证明，所以最后看出来就是一开始的那个东西。

rewrite 记号确实不太好理解。等到看自然数相关证明的时候会理解的更深刻些，可以拿来做归纳证明。

### Curry-Howard and Constructivity

Curry-Howard Isomorphism 其实是对应的 **constructive logic**，而非 classical logic。

构造逻辑大概说的就是，如果我们证明 $A \lor B$ 是真，那么我们一定是确切地知道 $A$ 和 $B$ 哪个为真。再比如说如果我们要证明存在一个满足某种性质的 $x$，那么一定要说明 $x$ 具体是谁。

而 nonconstructive reasoning 的基本要素是**排中律**：即对于任意命题 $P$，一定有 $P\lor\neg P$ 为真。比如说证明存在无理数 $a,b$ 使得 $a^b$ 为有理数：

> nonconstructive proof：
>
> 假设 $\sqrt{2}^{\sqrt2}$ 为无理数，则令 $a=\sqrt2^{\sqrt2},b=\sqrt2$，有 $a^b = \sqrt2^2=2$ 为有理数。
>
> 假设 $\sqrt2^{\sqrt2}$ 为有理数，则令 $a=\sqrt2,b=\sqrt2$。
>
> 证毕。

这就是一个应用排中律的典型例子。这里我们并不知道 $\sqrt2^{\sqrt2}$ 到底是不是无理数。

> constructive proof：
>
> 取 $a=\sqrt2,b=\log_29$，则 $a^b=  \sqrt2^{2\log_23} = 3$，证毕。

不过值得一提的是，不是所有定理都可以用构造逻辑完成证明的，比如说著名的停机问题，以及证明“$\pi + e$ 和 $\pi e$ 至少有一个是无理数”。

没看懂书上写的用 CH 同构处理 nonconstructive reasoning 的方法。咕着吧反正不重要。

## 自然数上的证明