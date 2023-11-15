---
title: 计算概论 A 课程笔记：Agda
date: 2023-11-15 17:00:01
tags:
  - Agda
  - 函数式编程
  - 笔记
categories: [笔记, 本科课程]
---

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