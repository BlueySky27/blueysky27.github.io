---
layout: post
title: "Paper Reading 2020.06.13"
date: 2020-06-13 19:00:00 +0900
categories: PaperReading 
---

<script type="text/javascript" async src="http://cdn.mathjax.org/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>


# Fast and Accurate LMS Solvers

[Fast and Accurate Least-Mean-Squares Solvers](https://arxiv.org/abs/1906.04705)

1. 之前组会的时候有人介绍的一篇NIPS2020的文章，关于LMS的，说实话不是很符合我的专业，但还是感兴趣地看了看，感觉……还是能看懂的！
2. 根据Caratheodory定理，线性回归中，n个d维向量可以由d+1个d维向量简单表示，且两组向量的重心相同。这样可以得到一个小得多的系数矩阵，也就可以大大提高线性回归的计算效率。
3. 传统算法算出Caratheodory集合的复杂度是$O(nd^3)$，而本文给出了一种$O(nd)$的算法。
4. 本文的新算法（算法2）基于聚类算法和矩阵运算中的sketches和coresets，大体流程是，先将所有的系数向量划分成几个聚类，然后对几个聚类的重心进行传统算法得到Caratheodory集合，然后只保留重心向量属于该集合的聚类 ，并进行新的一步迭代，直到剩下d+1个向量。
5. 该算法中的划分聚类数k是一个很重要的超参数，能够平衡speed与accuracy。
6. 应用这个算法到矩阵上，可以将一个 $n\times d$ 的矩阵简化为 $(d^2+1)\times d$ 的矩阵，并保持协方差矩阵不变。这里应该注意到无论是 $A^TA$ 中的 $A^T$ 还是 $S^TS$ 中的 $S^T$ 都只是一种加在系数矩阵上的weights而已，所以算法3本质上和计算Caratheodory集合的算法2并没有区别。
7. 本文的证明基本都只是集合求和式的代换，熟悉了几个变量的定义之后都很容易看懂。
8. 实验结果看不懂……

2020.06.13
