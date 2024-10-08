# Data[数据分析中的“数据”]

## 1. What is Data?[什么是数据？]

### Object & Attribute[对象和属性]

> **objects** and their **attributes**

- collection of **data objects** and their **attributes**

- an attribute is <u>a property or characteristic of an object</u>

  - **Attribute**：也被描述为variable/filed/characteristic/feature

- A collection of attributes **describe an object**

  - **Object**：也被称为record/point/case/sample/entity/instance

  <img src="C:\Users\耳东七月\AppData\Roaming\Typora\typora-user-images\image-20240326202456926.png" alt="image-20240326202456926" style="zoom:50%;" />

  可以参考数据库中的attributes

- Attribute values[属性值]
  - **numbers or symbols** assgned to an attribue





### Types of Attributes

- There are different types of attributes【有非常多属性类型】
  - **Nominal[标称属性]:** just names
    - 分类型的属性，反映对象属于哪一类，不具备有意义的序
    - ID numbers,eye color, zip codes
  - **Ordinal[序数属性(数据)]**: just names with ranking
    - 也反映数据对象属于哪一类，但是有程度上的差别
    - Rankings(比如薯片好吃程度基于1-10打分)，grades,height in {tall,medium,short}
  - **Interval[区间属性(数据)]**
    - 可以用数字定量地描述变量程度上的差异，计数零点的选择无关紧要
    - calendar dates， temperature in Celsius or Fahrenheit
  - **Ratio[比属性(数据)]**: differences, ratios are meaningful 
    - 在interval的基础上，计数零点有意义，即不仅变量之间的差值有意义，变量本身就有意义
    - temperature in Kelvin,length,time,counts





### Properties of Attribute Values[属性值的性质]

- 一个attribute的属性类型取决于它的值拥有下面的哪些属性
  - Distinctness:     $=,\ne$
  - Order:                $<,>$
  - Addition:            $+,-$
  - Multiplication:    $*,/$

- 属性类型和属性值性质的关系[一个更便于区分attribute type的方法]

  > 包含的属性由少到多

  - Nominal attribute： distinctness(only)
  - Order attribute:  distinctness & order
  - Interval attribute:  distincness, order & addition
  - Ratio attribute: **all 4 preoperties**

   



### Discrete and Continuous Attributes[离散/连续 的属性]

- Discrete Attribute
  - 拥有**有限/可数无限**的属性值集合
  - 经常表示为整数变量
  - 例子：zip codes, counts, or the set of words in a collection of documents
- Continuous Attribute
  - 以实数作为属性值
  - 在实际中，实数只能通过有限的位数来表示和测量
  - 通常被表示为浮点数变量
  - 例子：temperature，height，weight





## 2.Types of Data Set[数据集类型]

<img src="C:\Users\耳东七月\AppData\Roaming\Typora\typora-user-images\image-20240326220529576.png" alt="image-20240326220529576" style="zoom:50%;" />



### Record Data[记录型数据]

- Data that consists of a collection of records, each of which consists of a fixed set of attributes.由记录集合组成的数据，每个记录集合由一组固定的属性组成。

#### Data matrix[数据矩阵]

> 一种特殊的所有属性值均为数值的记录型数据

<img src="C:\Users\耳东七月\AppData\Roaming\Typora\typora-user-images\image-20240326221349693.png" alt="image-20240326221349693" style="zoom:50%;" />

- 整个数据对象可以看作是多维空间中的一个点（多维空间中的一个向量），而每一个维度表示着一个不同的属性
- 于是可以用一个矩阵来表示



#### Document Data

> 有点类似于inverted file index中的知识

- Each document becomes a 'term' vector.

  每个文档变成了一个“term”向量[单词向量]

  - each term is a component(attribute) of the vector
  - the value of each component is **the number of times**（频次） the corresponding term occurs in the document。每个组件的值为**对应的词在文档中出现的次数**。
  - 每个文档的vector值是对于该数据对象“字典库”的**频次索引向量**





#### Transaction Data

- A special type of record data, where
  - each record(transaction) involves a set of items
  - <img src="C:\Users\耳东七月\AppData\Roaming\Typora\typora-user-images\image-20240326222257326.png" alt="image-20240326222257326" style="zoom:50%;" />







### Graph Data[图数据]

#### graph data Ⅰ

- 例子：图论中的通用图，HTML连接

  <img src="C:\Users\耳东七月\AppData\Roaming\Typora\typora-user-images\image-20240326222442468.png" alt="image-20240326222442468" style="zoom:50%;" />



#### graph data Ⅱ

- 模型图

  <img src="C:\Users\耳东七月\AppData\Roaming\Typora\typora-user-images\image-20240326222502392.png" alt="image-20240326222502392" style="zoom:50%;" />





### Ordered Data[有序数据]

> Ordered data is when data is **collected over time**

- Sequential Data

  - sequences of transactions

    ![image-20240326222856720](C:\Users\耳东七月\AppData\Roaming\Typora\typora-user-images\image-20240326222856720.png)



- Genomic sequence data[基因序数据]

  <img src="C:\Users\耳东七月\AppData\Roaming\Typora\typora-user-images\image-20240326223333426.png" alt="image-20240326223333426" style="zoom:50%;" />



## 3. Data Quality[数据质量]

- 是什么样的data quality problems？
- 如何检测有关数据的问题？
- 我们能对这些问题做什么？

下面是两个data quality的例子



### Outlier[离群值/异常值]

- outeliers are data objects with characteristics that are considerably different than most of the other data objects in the data set。**离群值**是具有与数据集中大多数其他数据对象显著不同的特征的数据对象

  <img src="C:\Users\耳东七月\AppData\Roaming\Typora\typora-user-images\image-20240326225008328.png" alt="image-20240326225008328" style="zoom:50%;" />



### Missing values[缺失值]

- Reasons for missing values

  -  information is not collected

    (比如：人们拒绝给他们自己的年龄/体重)

  - Attributes may not be applicable to all cases

    (比如：儿童是不存在有年收入)



- Handling missing values
  - Eliminate Data Object
  - Estimate Missing Values





## 4. Data Preprocessing[数据处理方法]

> - Dimensionality Reduction[降维]
> - Feature subset selection[特征子集选择]
> - Feature creation
> - Discretization and Binarization[离散化和二值化]



### Dimensionality Reduction[降维]

#### Curse of Dimensionality[维数灾难]

- 当维数增加时，数据在其存在的空间中变得更加**稀疏**
- Definitions of density and distance between points,
  which is critical for clustering and outlier detection, become less meaningful。对于**聚类和异常点检测**至关重要的**密度**和**点间距离**的定义变得不那么有意义

#### Purpose of DR

- Avoid **curse of dimensionality**
- 减少数据挖掘(data mining)所需要的时间和存储
- 让数据能够更便于可视化
- 也许可以帮助消除不相关特征或减少**噪音**

#### Techniques of DR

- PCA：Principle Component Analysis[主成分分析]
- SVD：Singular Value Decomposition[奇异值分解]
- Others: supervised and non-linear techniques[监督与非线性的技术]
- 例子：PCA,SVD（These "create" new features via linear combination of existing features.它们通过现有特征的线性组合来“创造”新特征）



### Feature Subset Selection[特征子集筛选]

> Easier Method: just **throw away features!**
>
> 实际上也是一种数据降维的方法

- 可以丢掉的特征
  - **Rebundant features[冗余特征]**
    - **复制**一个或多个其他属性中包含的大部分或全部信息
    - 例子：买价格和所缴纳的销售税金额
  - **Irrelevant features[无关特征]**
    - 对于当下的数据挖掘任务来说不包含有效信息
      - 例子：学生的ID通常与预测学生的GPA任务无关



### Discretization[数据离散化]

- 一些数据挖掘方法只能用于**离散数据**

  $\Rightarrow$ 因此有时我们拥有讲实数值属性“离散化”的需求：将连续型属性值用离散值代替

  <img src="C:\Users\耳东七月\AppData\Roaming\Typora\typora-user-images\image-20240327164742588.png" alt="image-20240327164742588" style="zoom:50%;" />





## 5. Data Exploration and Visualization[数据探索和数据可视化]

 ### What is Data Exploration?[什么是数据探索]

- ***数据探索**定义：*数据探索是指数据分析的初始步骤。在数据探索过程中，数据分析师使用[数据可视化](https://link.zhihu.com/?target=https%3A//www.omnisci.com/learn/data-visualization)和统计技术来描述数据集特征（如规模，数量和准确性），以便更好地了解数据的性质。

- 数据探索的关键动机：

  - 有助于挑选合适的预处理和分析工具

  - 尽可能地利用人类对数据模式的理解认知

    人类处理视觉数据比数字数据更好，因而能够捕捉一些数据分析工具无法捕捉的信息

- 与EDA领域相关(Exploratory Data Analysis:探索性数据分析)

  <img src="C:\Users\耳东七月\AppData\Roaming\Typora\typora-user-images\image-20240327170015207.png" alt="image-20240327170015207" style="zoom:50%;" />

  http://www.itl.nist.gov/div898/handbook/index.htm





### Visualization[数据可视化]

- 定义：

  Visualization is the conversion of data into a visual or tabular format so that the characteristics of the data and the relationships among data items or attributes can be analyzed or reported。**数据可视化**是将数据转换为可视化或表格格式，以便可以分析或报告数据的特征以及数据项或属性之间的关系

- **数据可视化**是数据探索中最强大且受欢迎的方法技术

  - 人类在分析大量以视觉形式呈现的信息上有比较强的能力
  - 数据可视化能够发现一般的模式和趋势
  - 数据可视化可以检测异常值(outliers)和不寻常的模式



#### 数据的表示

- 数据信息向可视模板/格式的**映射**

  数据对象，及其属性，数据对象之间的关系 $\Rightarrow$ 图形化元素：点、线、图形、颜色

- 例子：

  <img src="C:\Users\耳东七月\AppData\Roaming\Typora\typora-user-images\image-20240327171143202.png" alt="image-20240327171143202" style="zoom:50%;" />



#### 一个经典数据集：鸢尾花样本数据集[Iris Sample Data Ser]

- 许多探索性数据技术都用鸢尾植物数据集来展示。

  <img src="C:\Users\耳东七月\AppData\Roaming\Typora\typora-user-images\image-20240327171430998.png" alt="image-20240327171430998" style="zoom:50%;" />

  http://www.ics.uci.edu/~mlearn/MLRepository.html

- 在“可视化”这一章，我们会用这一样本集来展示各种工具的实现结果



### Visualization Techniques[数据可视化工具]

#### Histograms[柱状图]

- 通常展示的是单元变量的值的分布

- 将这些值分成若干个箱子，并显示每个箱子中对象数量的条形图。

  <img src="C:\Users\耳东七月\AppData\Roaming\Typora\typora-user-images\image-20240327172242073.png" alt="image-20240327172242073" style="zoom: 67%;" />



#### Box Plots[箱形图]

- 箱形图是一种用作显示一组数据分散情况资料的统计图，图形如箱子，且在上下四分位数之外常有线条像胡须延伸出去而得名。

  ![image-20240327172131129](C:\Users\耳东七月\AppData\Roaming\Typora\typora-user-images\image-20240327172131129.png)

  <img src="C:\Users\耳东七月\AppData\Roaming\Typora\typora-user-images\image-20240327172313536.png" alt="image-20240327172313536" style="zoom:50%;" />



#### Scatter Plots[散点图]

<img src="C:\Users\耳东七月\Pictures\Screenshots\有道截屏翻译结果_1711531883009.png" alt="有道截屏翻译结果_1711531883009" style="zoom:50%;" />

- 鸢尾花属性的散点图阵列

  ![image-20240327173446439](C:\Users\耳东七月\AppData\Roaming\Typora\typora-user-images\image-20240327173446439.png)



#### Matrix Plots[矩阵图]

<img src="C:\Users\耳东七月\AppData\Roaming\Typora\typora-user-images\image-20240327173704488.png" alt="image-20240327173704488" style="zoom:50%;" />

- 适用于数据矩阵这种数据集
- 在数据对象按照类进行排序时用处比较明显
- 通常，属性会进行规范化/正交化来避免某一属性（向量）“占据”整个图形
- 相似/距离矩阵的图像在处理对象关系的可视化方面比较有用



- 鸢尾花数据集矩阵图

  <img src="C:\Users\耳东七月\AppData\Roaming\Typora\typora-user-images\image-20240327173757406.png" alt="image-20240327173757406" style="zoom:50%;" />



#### Parallel Coordinates[平行坐标]

> 多变量数据集的平行坐标呈现方式可以把多变量的交互作用显现在二维的平面图上

- 用于处理高维数据的属性值

- 用一组平行轴而非垂直轴来构图

- 每一个**数据对象**都被表示成一条曲线，其中每个数据对象的每个属性值都被绘制为对应坐标轴上的一个点。

- 通常，这些曲线独特地代表了某一种对象组合的类（至少对于一些属性值）

  而对于观察这样的组合，属性值的顺序非常重要



- 鸢尾花数据的平行坐标图

  <img src="C:\Users\耳东七月\AppData\Roaming\Typora\typora-user-images\image-20240327180443709.png" alt="image-20240327180443709" style="zoom:50%;" />



#### Contour Plots[等高线图]

> 等高线图：一种用于表示三维数据的二维图形，通过在二维平面上绘制等高线来表示数据的高度或密度分布。

- 在空间网格[spatial grid]上测量连续属性时非常有用
- 等高线图将平面划分成拥有相似值的多个区域
- 等高线，同时也是区分不同区域的边界线，将一系列等值点连接起来
- 最常见的等高图是海拔等高线图，当然在很多其他包括温度、适度、气压数据也经常用到等高图



- 一个例子：

  <img src="C:\Users\耳东七月\AppData\Roaming\Typora\typora-user-images\image-20240327180833019.png" alt="image-20240327180833019" style="zoom:50%;" />







## 6. Data Similarity[数据相似度]

### 数据相似度

- 在数据科学中，**相似性度量**是一种衡量数据样本如何相互关联或相互接近的方法。 另一方面，**相异性度量**是告诉数据对象有多少是不同的。

  此外，当相似的数据样本被分组到一个集群中时，这些术语通常用于聚类。 所有其他数据样本被分组到不同的样本中。它还用于分类（例如 KNN），根据特征的相似性对数据对象进行标记。另一个例子是当我们谈论与其他数据样本相比不同的异常值时（例如异常检测）。

- **相似性度量通常表示为一个数值**：当数据样本越相似时它就越高。 

  它通常通过转换表示为零和一之间的数字：

  - 0 表示相似度低（数据对象不相似）
  -  1 是相似度高（数据对象非常相似）



### 距离度量的指标

> 怎样才能算是一种“距离”度量？

1. 非负性： 对于任何两个不同的观测值p和q,d(p,q)$\ge$0, 且仅当 p = q 时，d(p,q)=0
2. 对称性：对于所有的p,q, d(p,q) = d(q,p)
3. “距离”的三角不等式：

距离度量是分类的**基本原则**，例如 k 最近邻的分类器算法，它测量给定数据样本之间的差异性。此外，选择距离度量将对分类器的性能产生很大影响。 

因此，计算对象之间距离的方式，也即**距离函数**，将在分类器算法的性能中发挥至关重要的作用。



### 距离函数

> *用于测量距离的技术取决于您正在处理的特定情况。*
>
>  例如，在某些区域，欧氏距离可能是最佳的并且对于计算距离很有用。 其他应用程序需要更复杂的方法来计算点或观测值之间的距离，例如余弦距离。
>
> 以下列举的列表表示计算每对数据点之间的距离的各种方法。



#### Euclidean distance[欧几里得距离，L2范数]



#### Manhattan distance [曼哈顿距离, L1范数]



#### Cosine distance/similarity [余弦距离/相似度]



#### Jaccard distance/similarity [杰卡德距离/相似度]
