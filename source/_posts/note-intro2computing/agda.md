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

## 概要

> Program testing can be used to show the presence of bugs, but never to show their absence!
>
> Edsger Dijkstra

现在的核心思想是，we can verify software correctness by writing mathematical proofs about it.

Agda 是一种函数式语言，事已至此先吃饭吧。

