---
title: 机器学习（2024Fall）课程笔记
date: 2024-09-09 20:09:12
tags:
  - 本科课程
  - 机器学习
---

本学期有幸选修了[张牧涵](https://muhanzhang.github.io/)老师开设的机器学习课程。张老师的课程讲述非常精彩且有特点，全程板书且穿插了不少 insights。

~~本来头两讲是在 iPad 上进行笔记记录的，第三节课发现直接用 Markdown 可以跟得上，遂将笔记整理于此。~~

~~笔记初步分 lecture 整理，以后可能会以知识模块进行重构。~~ 到目前为止已重构完成。

目录：

- [01-线性回归](/note-ml2024fall/linear-regression/)
- [02-逻辑回归](/note-ml2024fall/logistic-regression/)
- [03-偏差方差分解](/note-ml2024fall/b-v-decomposition/)
- [04-支持向量机](/note-ml2024fall/svm/)
- [05-表示定理](/note-representer-theorem/)


课程大纲：

| 1    | 线性回归（Linear Regression）                                | 3    | 经验风险最小化, 矩阵求导，线性回归闭式解，岭回归和Lasso，最小二乘几何解释 | ERM, Matrix Derivatives, Closed-form solution to Linear Regression, Ridge Regression and Lasso, Geometric view of Least Squares |
| ---- | ------------------------------------------------------------ | ---- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2    | 逻辑回归（Logistic Regression）                              | 6    | 最大似然原则，交叉熵损失，线性可分，逻辑回归凸函数的证明，平方损失函数分类的缺点，Softmax Regression，线性回归的最大似然解释，岭回归的最大后验解释 | MLE, Cross Entropy, Linear Separable, Proof of the convexity of Logistic Regression, Drawbacks of squared loss for classification, Softmax Regression, MLE of Linear Regression, MAP of Linear Regression |
| 3    | 偏差-方差分解（Bias-Variance Decomposition）                 | 3    | 偏差方差分解，模型选择，奥卡姆剃刀原理                       | Bias-Variance Decomposition, Model Selection, Occam’s Razer  |
| 4    | 支持向量机与对偶理论（Support Vector Machine and Dual Theory） | 9    | 约束优化问题，拉格朗日乘子法，KKT条件，支持向量机主形式，对偶形式，对偶问题的SMO算法，核方法，松弛变量 | Constrained optimization, Lagrange Method, KKT conditions, Primal and Dual forms of SVM, SMO, Kernel methods, Slack variables |
| 5    | 表示定理（Representer’s Theorem）                            | 3    | 再生核希尔伯特空间，表示定理及证明，利用表示定理解释岭回归   | RKHS, Representer’s Theorem, Revisiting Ridge Regression     |
| 6    | 学习理论（Learning Theory）                                  | 3    | PAC理论，成长函数，VC维，VC泛化界                            | PAC Learning Theory, Growth function, VC-dimension, VC Generalization Bound |
| 7    | 树模型和集成学习（Tree Models and Ensemble Learning）        | 6    | 信息熵，信息增益，增益率，基尼系数，决策树，回归树，连续特征，Bagging, 随机森林，Boosting, AdaBoost, GBDT | Entropy, Information gain, Information gain ratio, Gini-index, Decision Tree, Bagging, Random Forest, Boosting, AdaBoost, GBDT |
| 8    | 高斯过程（Gaussian Process）                                 | 3    | 多元高斯分布，随机过程，高斯过程回归，贝叶斯优化             | Multivariate Gaussian Distribution, Random Process, Gaussian Process Regression, Bayesian Optimization |
| 9    | 图模型基础（Graphical Models Basics）                        | 3    | 贝叶斯网络，条件独立性，D-separation，无向图模型             | Bayesian networks, Conditional independence, D-separation, Unconditional graphical models |
| 10   | 无监督学习（Unsupervised Learning）                          | 9    | 主成分分析，混合高斯模型，EM算法，变分自编码器，扩散模型     | PCA, Mixture of Gaussians, Expectation-Maximization, VAE, Diffusion Models |
