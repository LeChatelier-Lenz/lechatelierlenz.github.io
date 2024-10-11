---

title: Transformer 解析
date: 2024-10-11 21:04:51
tags: AI
mathjax: true

---

# Transformer原理解析

> 本篇文章致力于从网络上的各种讲解文章/视频，来解析Transformer模型的原理
>
> 参考内容：
>
> - 博客：[The Illustrated Transformer – Jay Alammar – Visualizing machine learning one concept at a time. (jalammar.github.io)](https://jalammar.github.io/illustrated-transformer/)
>   - 中文翻译版：[图解Transformer | The Illustrated Transformer-CSDN博客](https://blog.csdn.net/qq_36667170/article/details/124359818)
> - 视频：[How large language models work, a visual intro to transformers | Chapter 5, Deep Learning (youtube.com)](https://www.youtube.com/watch?v=wjZofJX0v4M)



## 背景介绍

Transformer使用了现代神经网络常用的一种方法——Attention机制，Transformer本身就是一种用注意力机制来提高模型训练速度的模型。

> [[1706.03762] Attention Is All You Need (arxiv.org)](https://arxiv.org/abs/1706.03762) 这一篇注意力机制的提出论文开启了后续GPT以及当前AI时代的先声

Transformer在某些特定任务上性能比谷歌的机器翻译模型更为优异。其优点在于**并行化计算**。并且谷歌云也推荐使用transformer作为参考模型来运行他们的TPU云服务。所以让我们来把它拆解开看一下它是如何运行的。

> [官方版TensorFlow实现](https://github.com/tensorflow/tensor2tensor)
>
> [哈佛大学NLP组Pytorch实现](http://nlp.seas.harvard.edu/2018/04/03/attention.html)



## 宏观角度的考察

从神经网络开始，人工智能的模型功能很大程度上一直类似于一种实现提取/分类功能的**黑箱**

- 而Transformer在宏观上与这些模型一样，也是一个**黑箱**，它能够实现更精准的翻译【如下图】，或者根据上下文给出文字补充（GPT-3）
  <img src="https://jalammar.github.io/images/t/the_transformer_3.png" alt="transfomer"/>
  - 而本篇文章的目的就在于，一步步地拆开这个黑箱的结构，得到一个更清晰的理解
  - 不过，就像剥洋葱一样，我们需要一层一层慢慢剥开

首先从最简单的拆分来看，Transformer可以被划分为编码器和解码器两个部分
<img src="https://jalammar.github.io/images/t/The_transformer_encoders_decoders.png" style="zoom:60%" />

> 编**码**器和解**码**器： 从机器翻译的角度来看，是将语言文字进行编码和解码的机制

再往下一步拆分之后，我们可以进一步进入以下好几层的解释阶段，每个阶段代表了Transformer设计上的一种巧思

- 编码器和解码器实际上是**多层**的,输入通过encoder层层转递形成最终编码，而decoder又通过层层参数调整形成最终的答案
  <img src="https://jalammar.github.io/images/t/The_transformer_encoder_decoder_stack.png" style="zoom:50%" />

  - 层数并没有特别的讲究
  
    > 不过层数是一个模型可调整的超参数
  
  - 每一个编（解）码器单元彼此之间**结构相同**，但是权重不同，参数**彼此不共享**
  
- 编码器和解码器的各自内部结构：**自注意力层**+**前馈神经网络层(Feed Forward NNs)**（后者在3B1B讲解的GPT3中直接划为MLP）
  <img src="https://jalammar.github.io/images/t/Transformer_decoder.png" />

  - 自注意力层的输出会传递给一个前馈神经网络
    
  - encoder和decoder彼此之间结构不同之处在于decoder中间还有一层**Encoder-Decoder Attention**,是用来体现输入彼此之间上下文关系对输出结果的影响的
    
    > jalammar提到了seq2seq模型中的attention层，认为比较相似



## 更进一步---分析张量结构

> 我们需要知道，**文字**是怎么样转化为张量，而这些encoder和decoder又是如何对张量进行处理最终产出想要的答案的
> 实际上，“文字”并不是非常准确，一般来说我们的输入会划分为若干**token**，而token可能是文字/单词，标点符号，变体标志(拉丁语系的人称、时态变化标志)等

和传统的NLP模型一样，我们需要把输入的token通过词嵌入(embedding)转化为**对应的向量**

<img src="https://jalammar.github.io/images/t/embeddings.png" />

所有的编码器都是统一的输入向量规格（一般是512维，为了便于展示，图像中会用更少的格子），除了第一个编码器接受的是embedded input以外，后续的encoder处理的都是前一个encoder的输出

- 同理，输出规格和输入规格也是一致的

> 向量长度也是一个可以调整的**超参数**



### Encoder编码器

向量会依次通过编码器组件的两个层

<img src="https://jalammar.github.io/images/t/encoder_with_tensors.png" style="zoom:40%" />

- 我们可以观察到一个细节，即输入中每个位置的单词是独立进行各自的流程的（有各自的流通方向）

  - 这是Transformer实现高并行性的一个关键属性
  - 并且，**只有在自注意力层当**中才会有输入之间的依赖，FF层并不需要，因此在流过FF层时，各个向量可以**并行计算**

  <img src="https://jalammar.github.io/images/t/encoder_with_tensors_2.png" />



- 从这里开始，我们将走进Transformer的第一个核心内容：注意力层



## Self-Attention【自注意力】

> 自注意力，实际上不是我们从人的角度来说那么直觉的概念。
>
> 实际上对机器来说，可以粗略地理解为：**“机器需要注意到自己在说什么，以更好理解自己说的每个词的内容”**

### 介绍引入

以一个例子引入，我们来理解一下注意力机制：

- 假如我们要翻译下面的这句话：

  `“The animal didn't cross the street because it was too tired”`

- 对机器来说，我们需要具备一种**机制**让它意识到it和street/animal之间的联系（关系强弱），从而确认it在这句话背景下的意思

用更机器的话来说：当模型编码每个位置上的单词的时候，自注意力的作用就是，看一看输入句子中其他位置的单词，试图寻找一种对当前单词**更好的编码方式**。（更能表达其信息维度的编码方式）

如下是一个利用Tensor2Tensor的例子，代表在最后一层encoder组建中对it进行编码的时候，注意力机制会更关注The animal，并将其融入到it的编码中

<img src="https://jalammar.github.io/images/t/transformer_self-attention_visualization.png" />

> 连线粗细代表权重大小/关系强弱



### 自注意力机制

> $Z=softmax(\frac{QK^T}{\sqrt{d_k}})V$

#### 向量解释

##### 第一步： 为每一个输入都生成一组query，key，value向量

> ”啊？怎么生成的？“
>
> 将输入的词嵌入向量和三个已配好的权重矩阵**相乘**，你可以理解为利用三个特殊的**矩阵函数**得到的输出。而这些权重矩阵都是模型训练阶段训练出来的（反向传播）
>
> 在同一个编码器组件中，一组向量使用**相同的权重矩阵**，即相同的$W^Q,W^K,W^V$

一般来说，这样的三组向量维数会相对更小。不是说**必须**要比encoder的输入维数小，这么做是为了让多头注意力的计算更稳定

<img src="https://jalammar.github.io/images/t/transformer_self_attention_vectors.png" style="zoom:50%;" />

> 如上图，X为输入，通过与右侧W权重矩阵相乘，得到一系列qkv向量



##### 第二步：计算“注意力得分”

> query, key, value的作用是什么？

[待完善]
