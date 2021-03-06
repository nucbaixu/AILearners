# 【2019斯坦福CS224N笔记】（11）ConvNets for NLP

## 从RNN到CNN

当我们听到卷积神经网络（CNN）的时候，我们通常会想到计算机视觉。而CNN是图像分类的重大突破，并且是当前很多计算机视觉系统的核心。近年来，人们也开始将CNN运用到NLP的问题中，并且得到了一些成果，例如人们将CNN成功的运用到了文本分类任务中。

## 什么是卷积

经典的CNN一般由卷积层、池化层和全连接层组成。而CNN本质上来说就是几个卷积层加上非线性函数，像ReLu或者tanh。在传统的神经网络中，我们将每个输入神经元与下一层连接。这个叫做全连接层或者仿射层。在CNN中，我们不那么做。

相反，我们用输入层上的卷积来计算输出。这就导致了局部连接，其中每个输入层的区域都是输出层的神经元连接在一起的。每层都使用了filter，通常会有成千上百的类似于下面有小框框的图那样的操作。理解卷积，最简单的一种方式就是将它的滑动窗口函数放到一个矩阵上，如下图就是一个清晰的卷积可视化结果。

![](../../../img/nlp/cs224n/11ConvNets&#32;for&#32;NLP/v2-6428cf505ac1e9e1cf462e1ec8fe9a68_b.gif)

这个滑动的小框一般被称为 kernel， filter，或者是feature detector。这里我们用的是3X3的小框，滑动时对应元素相乘，然后再相加。

## 如何运用到NLP

与图像像素不同，对于绝大多数NLP的任务，输入都是矩阵形式的句子和文档。矩阵的每一行都对于这一个单词，但是也可能是一个字符。因此对于文本来说这里是1维的。也就是，每一行都是一个表示一个单词的向量。通常来说，这些向量都是word embedding（低维表示），比如word2vec或者GloVe，但是他们也可能是one-hot向量（对应于一个词典）。

举个例子：对于一个7个单词的句子用一个4维的embedding，我们可能需要7X4的matrix作为我们的输入。这就是我们的“image”。如下图所示，我们可以使用一个kernel来卷积该文本，从而得到一个五维的结果：

![](../../../img/nlp/cs224n/11ConvNets&#32;for&#32;NLP/微信截图_20190730221328.png)

由此可以看出，我们经过卷积操作，不经意间矩阵的维度降到了5维，但很多时候这并不是我们想要的结果。因此我们可以使用padding进行补零，如下图所示:

![](../../../img/nlp/cs224n/11ConvNets&#32;for&#32;NLP/微信截图_20190730221858.png)

当然，我们还可以使用多个卷积核来进行卷积。例如，使用下图中三个不同的卷积核：

![](../../../img/nlp/cs224n/11ConvNets&#32;for&#32;NLP/微信截图_20190730222215.png)

除了卷积操作外，CNN中还有一个关键的部分：pooling层，通常用在卷积层后面。pooling层重采样他们的输入，最通常的方式为max pooling。

为什么要pooling？有很多原因。pooling的一个特性就是它提供了一个固定大小的输出矩阵，这通常对于分类来说是必要的。例如，如果你有1000个filters并且对每个filters都使用max pooling，那么久会得到一个1000维的输出，不管你的filter以及输入的大小。这就使得你可以使用长度变化的句子以及尺寸变化的filters，但是总是会输出相同维度的东西给分类器。

Pooling也能够减少输出的维度，但是也有希望保存最突出的信息。比如说，我们在处理文本时，经常会有一些诸如a/the一些没有实际意义的虚词。通过pooling，我们就可以有效的过滤到这些词，更好的保存有效信息。当然，在实际应用中，除了max pooling，我们还可以使用k-Max pooling来保留更多的特征。

## CNN与文本分类

给CNN最适合的任务就是分类。例如Zhang and Wallace在2015年发表的一篇名为“A Sensitivity Analysis of (and Practitioners’ Guide to) Convolutional
Neural Networks for Sentence Classification”的文章，采用了下图所示的CNN结构：

![](../../../img/nlp/cs224n/11ConvNets&#32;for&#32;NLP/微信截图_20190730223828.png)

如图所示，该模型非常容易理解。首先，对于输入的句子包含7个单词，其中每个单词的维度为5。然后经过6个卷积核的卷积，进而通过max pooling操作并将其拼接起来，最后通过softmax对文本进行分类。

## 参数

构建一个CNN框架意味着有很多参数要选，输入的表示(Word2Vec, GloVe, one-hot), 卷积filter的数量以及大小，pooling策略(max, average)以及激活函数(ReLu, tanh。而且理想的filter大小是很重要的。除此之外，还有一个一个非常重要的技术——Dropout，这是一种常见的正则化技术，可以有效的解决过拟合的问题。

除此之外，这里还有一些问题可以思考，目前我们所看到的CNN在NLP中的应用，大部分的网络结构都非常浅，我们可不可以使用更深层的网络？如果可以，应该采取什么的方式呢？

喜欢的童鞋记得分享给别的小伙伴哈。AIMI-CN AI学习交流群【1015286623】 获取更多AI资料扫码加群：

<div align=center><img src="../../../img/otherImages/gkj/QRcode_qq.png" /></div>

分享技术，乐享生活：我们的公众号每周推送“AI”系列资讯类文章，欢迎您的关注！

<div align=center><img src="../../../img/otherImages/gkj/QRcode_wechart.png" /></div>