---
layout: post
title: "Sketch-BERT"
date: 2020-06-17 23:00:00 +0900
categories: PaperReading
---


# Sketch-BERT

[Sketch-BERT: Learning Sketch Bidirectional Encoder Representation from Transformers by Self-supervised Learning of Sketch Gestalt](https://arxiv.org/abs/2005.09159)

1. 看这篇的原因应该是CVPR2020的paper list出来了，然后就随机地在看一些caption或者transformer相关的文章。

2. 本篇论文的task应该是素描相关的recognition和retrieval，还有一个sketch gestalt。Gestalt是个德语词，中文翻译是“格式塔”，sketch gestalt的任务就是根据没有被遮挡的部分素描来恢复素描的整体。所以，可以说sketch gestalt和BERT论文中提到的两个pre-training任务有相似之处，因此也就非常自然地引出了self-supervised learning的方法。

3. 一张素描（sketch）可以由一系列线条（stroke）组成，而线条则由一系列点组成，作者对线条进行了三种embedding处理：
    - 作者这里给将每个点用一个五维向量 $(\Delta x,\Delta y,p_1,p_2,p_3)$ 表示，前两者代表该点与线条中上一个点的坐标差（这里作者归一化了），后三者则为一个onehot向量，$p_2=1$ 意味着该点为线条的起点，$p_3=1$ 意味着该点为线条的终点，$p_1=1$ 则对应线条中的其他点。然后用一个嵌入矩阵得到point embedding：

        $$E_{pt}=W_{pt}(\Delta x,\Delta y,p_1,p_2,p_3)^T$$

    - 将每根线条限定为250个点以下，并用onehot向量表示每点的序列位置，并嵌入得到positional embedding：

    $$E_{ps}=W_{ps}\textbf{1}_{ps}$$

    - 对一张素描中的线条做同样的处理，限定50根线条并用onehot向量表示序列位置，嵌入得到stroke embedding：

    $$E_{str}=W_{str}\textbf{1}_{str}$$

    - 于是得到最终的表示向量：

        $$E=E_{pt}+E_{ps}+E_{str}$$

    - 然后使用三层全连接层网络将原有的128维向量refine成768维向量。
    
4. 网络结构使用weight-sharing multi-layer bidirectional transformer，具体结构参照下图。

    ![](https://github.com/BlueySky27/blueysky27.github.io/blob/master/static/img/Annotation20200617224122.png)

5. 之后的训练相对来说比较简单。受BERT的启发，作者先用Sketch Gestalt作为预训练的task，然后再训练Sketch Recognition\Retrieval\Gestalt的task。
    - 预训练为将一张素描中15%的点的位置信息和状态信息掩盖（mask）住，然后用Sketch-Bert进行预测。
    - 得到768维向量后再用与之前refine网络对称的reconstruction网络进行decode得到5维向量，从而恢复原素描。
    - 之后的Recogonition和Retrieval的训练都可以参照上图的右半部分，相比预训练区别是这里的输出中多输出Class Label或者Retrieval Feature。

6. 本文的方法在两个数据集上均达到了SOTA，大大超越了之前基于CNN的方法。难道万物皆可Transformer化？毕竟素描是可以与现实世界中的object对应上的。期待进一步的结果。
