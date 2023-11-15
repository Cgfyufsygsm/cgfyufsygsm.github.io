---
title: Latex 细碎笔记整理
date: 2023-09-13 16:52:31
tags:
  - 笔记
  - Latex
categories: 笔记
password: 20041031
---

##  latex 符号需要注意的

双引号和单引号，左引号均使用键盘左上角的\`键实现，右引号使用冒号右边的单引号实现。例如

```latex
``I love you''
```

## 宏包

在导言区使用 `\usepackage` 命令。

值得注意的是，`amsmath` 宏包提供无编号的公式环境，如

```latex
\begin{equation*}
E=mc^2
\end{equation*}
```

以及 `\min` 等常见命令。不常见命令的正体排版需用 `\operatorname`。

## 文章结构

### 开头

在导言区使用 `\title{Your Title}` 和 `\author{The Author}` 来告诉 $\LaTeX$ 文章的标题和作者，然后在正文区调用 `\maketitle` 命令。在 `\maketitle` 之后，如有摘要，可使用摘要环境，如

```latex
\documentclass{article}

\title{Title}
\author{ME}
\date{\today}

\begin{document}
\maketitle

\begin{abstract}
Here goes the abstract.
\end{abstract}
\end{document}
```

### 分节

使用 `\section` 和 `\subsection` 命令。

默认是标号了的，使用 `\section*` 即使其不参与标号。

### 标签与交叉引用

使用 `\label` 可以标记段落和公式，调用时使用 `\ref{}` 和 `\eqref{}`（`amsmath` 宏包），如下：

```latex
\documentclass{article}
\usepackage{amsmath} % for \eqref

\begin{document}

\section{Introduction}
\label{sec:intro}

In Section \ref{sec:method}, we \ldots

\section{Method}
\label{sec:method}

\begin{equation}
\label{eq:euler}
e^{i\pi} + 1 = 0
\end{equation}

By \eqref{eq:euler}, we have \ldots
\end{document}
```

标签的命名可以随意，但是最好采用规范形式，如对于 section 标记就采用 `sec:xxx` 的方式。

