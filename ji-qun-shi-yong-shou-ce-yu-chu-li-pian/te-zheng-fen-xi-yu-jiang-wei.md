---
description: 作者：唐元博         时间：2020-4-19
---

# 特征分析与降维

### 前言

根据[Outlin](outline.md)中的分析，我们知道原始的大数据集中包含大和脏两大特点，在本页我们主要针对特征维度高这一特点作分析和处理，我们的目标是分析高维特征、并尝试在保留数据质量的前提下尽可能降低特征维度，达到降低数据量、便于后续程序分析的效果。

### 分析

首先，我们需要对数据的高维特征作分析，因为只有对于这些特征有相当清晰的认识，才可以作下一步的特征抽取或转换。在[数据统计](shu-ju-tong-ji.md)这一页中我们介绍了数据的基本统计操作，包括对于特征缺失率、均值、方差、最大最小值的统计，根据这些信息，再结合背景语义，我们可以大概对某一维特征的质量、规模等情况有一个基本的认识。

接下来，通过normalization方法我们可以将不同尺度的特征缩放到同一尺度下，通过计算特征之间的相关系数，我们可以知晓特征之间的关联性，为下一步降维作准备。

### 降维

对高维数据降维有两种主要方式：特征抽取和特征转换，前者意味着使用一部分相对重要的特征而丢弃那些不太重要的，后者则是将全体特征投影到一个新的空间中，通过去除特征之间的相关性进行数据压缩（PCA是其中典型的方法）。大体上前者的优势在于易于理解、解释，后者则有着更好的压缩性价比（即在同样信息损失的情况下压缩率大大增加）。

这里给出在spark平台上对数据进行PCA降维的示例代码，为了简便，我们采用一个3\*5的矩阵举例。代码运行在[http://10.129.2.159:9090](http://10.129.2.159:9090/)的zepplin平台上。

```python
#首先指定编译器为pyspark
%pyspark 
#导入相关包
from pyspark.ml.linalg import Vectors 
from pyspark.ml.feature import PCA 
#设定数据 并转化为spark的dataframe格式数据
data = [(Vectors.sparse(5, [(1, 1.0), (3, 7.0)]),), 
        Vectors.dense([2.0, 0.0, 3.0, 4.0, 5.0]),), 
        (Vectors.dense([4.0, 0.0, 0.0, 6.0, 7.0]),)] 
df = spark.createDataFrame(data,["features"]) 
#直接调用PCA函数，k=2意思是取前两个最大的主成分
pca = PCA(k=2, inputCol="features", outputCol="pca_features") 
#使用先前指定的data计算映射model
model = pca.fit(df) 
#将数据映射到低维空间，并打印第一维数据
model.transform(df).collect()[0].pca_features
#运行结果为
#DenseVector([1.6486, -4.0133])
```


