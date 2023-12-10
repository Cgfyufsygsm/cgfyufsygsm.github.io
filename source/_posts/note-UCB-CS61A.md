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

## Lecture 2: Functions

`add (2, 3)`，`add` 为 operator，2 和 3 为 operand。

operator 和 operand 都是 expression。评估（evaluate）其值的时候，先 evaluate operator 然后 operands，再将 operator 对应的值对应的函数（function）**apply** 到 operand 对应的值上。

用 `a, b = exp1, exp2` 类型的语法可以批量赋值。**注意，赋值运算符的规则是先把右边的全部算完了再 bind 到左边的 name**，比如说下面这段代码：

```python
>>> a=1
>>> b=2
>>> a = 1
>>> b = 2
>>> b, a = a + b, b
>>> print(a, b)
2 3
```

先算出来 `a + b = 3, b = 2`，然后再 bind 到左边的 b 和 a 上。

然后，注意我们是可以在 python interpreter 里面令

```python
>>> max
<built-in function max>
>>> max=3
>>> max
3
```

以及，对全局变量调用：

```python
>>> def area(): # 注意这里的冒号
...   return pi * radius * radius # 注意这里一定要有缩进
...
>>> radius=  1
>>> area
<function area at 0x0000022FCC288FE0>
>>> area()
3.141592653589793
>>> radius = 10
>>> area()
314.1592653589793
```

Python 查找命名的时候优先在当前作用域内查找，所以如下的代码是被允许的：

```python
def square(square):
    return square * square
```

但是这样对全局变量修改是不被允许的：

```python
x = 10

def f():
    x = 3
    print(x)

f()
print(x)
```

会输出 3 和 10。因为函数里面是独立的作用域，`x = 3` 相当于声明了一个新的局部变量。

如果想要引用全局变量，使用 global 关键字：

```python
x = 10

def f():
    global x
    x = 3
    print(x)

f()
print(x)
```

## Lecture 3: Control

- 没有显示返回值的函数视作返回 `None`。Python 解释器不会打印 `None`。
- `python3 -m doctest example.py` 可以检验函数是否满足 docstring 的需求。

- An environment is a sequence of frames。对于 `square(square(4))`，其有 3 个 frames。注意到我们查找 name 对应的含义的时候是递归向上查找的。

  A name evaluates to the value bound to that name in the earliest frame of the current environment in which that name is found.

关于 statements：A statement is executed by the interpreter to perform an action

```python
<header>:
    <statement>
    ...
<separating header>:
    <statement>
    ...
...
```

比如说 def statement。

while 和 if 的语法也都很简单，直接举个例子吧：

```python
def hailstone(n):
    """Print the hailstone sequence starting at n and return its
    length.

    >>> a = hailstone(10)
    10
    5
    16
    8
    4
    2
    1
    >>> a
    7
    """
    "*** YOUR CODE HERE ***"
    len = 1
    print(n)
    while n != 1:
        if n % 2 == 0:
            n = n // 2
        else:
            n = 3 * n + 1
        len = len + 1
        print(n)
    return len

```

## Lecture 4: Higher-Order Function

类似 Haskell 地，Python 也支持将函数作为参数或返回值。比如说

```python
def id(x):
    return x

def cube(x):
    return x * x * x

def summation(n, f):
    sum, k = 0, 1
    while k <= n:
        sum, k = sum + f(k), k + 1
    return sum
```

（比 C 系列语言方便多了啊，，，）

能够方便地提升抽象的层级。

Lambda 函数同样是被支持的：

```python
square = lambda x: x * x

f = lambda a, b: a - b
```

- Lambda 函数只支持简单的表达式，不支持内含 statement
- Lambda 函数不会 **create an intrinsic name**，只是将 `=` 前面的 name 和对应的内容 bind 在一起，而 `def` 创建的会
- 好像就没啥区别了

> 为何我们无法使用一个形如 `if_(<condition>, <expr1>, <expr2>)` 的函数来代替 `if` 呢？
>
> 究其原因，Python 在执行 call 表达式的时候，是要全部 eval 出 operand 的值的。这就使得 expr1 和 expr2 都要被评估，这是通常情况下我们不希望的——if 的分支们只会被执行一个。
>
> 不过好的是，`and` 和 `or` 运算符仍具有短路性质。

## Lecture 5 Environments

注意到在查找名字的时候是沿着 parent frame 往上找的。

```python
def print_sums(n):
    print(n)
    def next_sum(k):
        return print_sums(n + k)
    return next_sum
```

这是一个很神秘的例子。

```bash
>>> print_sums(1)(3)(5)
1
4
9
<function print_sums.<locals>.next_sum at 0x000001F9D8688EA0>
```

但是确实是很有效的。

所以，Python 也支持类似于 curried functions 的想法。

```python
def make_curry2(f):
    def g(x):
        def h(y):
            return f(x, y)
        return h
    return g
```

```bash
>>> from operator import add
>>> m = make_curry2(add)
>>> m(2)(3)
5
```

不过这个的功能肯定是没有 Haskell 的强大的。
