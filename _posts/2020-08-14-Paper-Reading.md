---
layout: post
title: "CIDErBtw"
date: 2020-08-14 17:30:00 +0900
categories: PaperReading
---


# Compare and Reweight: Distinctive Image Captioning Using Similar Images Sets

[Compare and Reweight: Distinctive Image Captioning Using Similar Images Sets](https://arxiv.org/abs/2007.06877)

1. 本篇论文是ECCV2020 Oral，主要贡献在于提供了一种新的metric，使得算法可以在保持SOTA准确率的前提下，生成更加distinct的语句。
2. 如何生成多样化的语句？近来各种方法层出不穷，包括GAN、Scene Graph和多步LSTM等等。作者这里提出的方法是，引入一种新的metric，使得distinctiveness在训练和生成的时候都在损失函数中占有一席之地，从而使得模型有着生成更加distinct的语句的倾向。我个人是第一次看到这种真正落地的研究，所以觉得这种新metric的提出与成功是这篇文章得以成为ECCV Oral的原因。
3. 本文提出的新的metric基于旧有的CIDEr。当前使用的MSCOCO Caption数据集，每张图片会对应5个语句，传统上这5个语句在训练中是一视同仁的，但作者认为这5个语句本身就体现了distinctiveness，所以应该赋予他们不同的权重。那么怎样赋予不同的权重呢？作者在此先引入一张图片的相似集合（similar set）的概念，对于当前需要计算语句权重的一张目标图片 $I_0$，首先利用embedding vector来计算图片 $I_i$ 和 $I_j$ 之间的相似度：

    $$S(I_i,I_j)=\max_{k\in\{1,\dots,N\}}g_r(I_i,c_k^j),\quad g_r(I_i,c_k^j)=\frac{\phi(I_i)^T\theta(c_k^j)}{||\phi(I_i)||\cdot||\theta(c_k^j)||}$$

    这里 $\phi(\cdot)$ 和 $\theta(\cdot)$ 两个函数对应图片和语句的embedding function，利用生成的embedding vector计算余弦距离得到图片与语句之间的相似度$g_r$，最后再根据图片 $I_i$ 与图片 $I_j$ 的所有语句$c_k^j$ 之间的相似度的最大值来作为两张图片的相似度。这里有个小问题就是这个相似函数似乎不是对称的，所以可能定义一个新形式（比如取二者的平均值）会好一些（纯脑测）。于是我们可以这么计算任意一对图片的相似度，并将和目标图片相似度最高的K张图片定为目标图片的相似集合。

    然后作者就引入了名为CIDErBtw（Between-set CIDEr）的新metric，对于目标图片 $I_0$ 的一条语句 $c$ ，基于相似集合 $\{I_1,I_2,\dots,I_K\}$ 我们有：

    $$CIDErBtw(c)=\frac{1}{KN}\sum_{k=1}^K\sum_{n=1}^Ng_c(c,c_n^k)$$

    这里的 $g_c$ 是CIDEr函数，注意和上面计算相似度的区别。

4. 根据CIDErBtw，我们可以得到训练时目标图片 $I_0$ 的全部N个ground truth语句的权重：

    $$v_i=CIDErBtw(c_i^0),\quad w_i=\lambda_w-\alpha_w\frac{v_i}{max_i(v_i)}$$

    这里的 $\lambda_w$ 和 $\alpha_w$ 均为正的超参数，以及从可以看出，CIDErBtw分数越低的语句、和相似集合中的图片越不相似的语句，权重也就越高。由于作者不是只使用CIDErBtw，传统metric仍然也用，所以这里分数越低并不意味着语句会偏离图片，而只是语句在正确的基础上更加distinct了。

5. 作者的模型基本沿用Image Captioning的基本框架（没有用到比较复杂的网络结构，而是常用的att2in、UpDown和Transformer等），只是对训练过程进行了修改。传统的Image Captioning的训练框架是先在XE loss下进行监督学习训练，然后在强化学习的框架下（SCST）进行训练。作者这里的改动有三点，前两点是基于此前得到的图片权重 $w_i$ 对监督学习的损失函数和强化学习和回报函数进行修改：

    $$\mathcal{L}_{XE}=\sum_{i=1}^Nw_iL_{XE}(c_i^0)\\ \bar\mathcal{R}(c^*)=\frac{1}{N}\sum_{i=1}^Nw_ig_c(c^*,c_i^0)$$

    然后是在使用CIDEr的回报函数基础上引入CIDErBtw：

    $$\mathcal{R}(c^*)=\bar\mathcal{R}(c^*)-\alpha_rCIDErBtw(c^*)$$

    可以看到这些修改大体都是比较合理而简单的修改。

6. 由于这篇文章属于开创之作（引入新metric），所以不太好评价生成的语句质量。不过，本文确实给出了一种计算distinctiveness的好方法，给了各种相关研究一个相对比较客观的标准。
7. 我觉得本文一个问题是那个embedding function，这个是后面计算权重和新测度的标准，但是作者引用的是一个用得不是很多的VSE++的方法。而且我总觉得，如果embedding function做得特别好的话，感觉image captioning就已经做完了，后面还要做这些事情就显得很冗余……不过问题就是目前的embedding function普遍效果一般吧，还需要更多的研究。
