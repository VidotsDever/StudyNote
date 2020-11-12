MLlib主要包含三个部分：

* 底层基础：包括Spark的运行库、矩阵库和向量库；
* 算法库：包含广义线性模型、推荐系统、聚类、决策树和评估的算法；
* 实用程序：包括测试数据的生成、外部数据的读入等功能；



**基本数据类型**



**本地向量**：可以用来对比机器学习中类别标签列

* dense稠密性向量(所有数据都存储)：

```
val dense = Vectors.dense(9, 5, 0, 2, 7)
```

* sparse稀疏性向量(只存储非零数据)：

```
val sparse = Vectors.sparse(10, Array(1, 2, 3, 9), Array(1, 1,1, 1))
第一个参数：向量的总数；
第二个参数：非零值的索引；
第三个参数：非零值的值；

或者
val sparse = Vectors.sparse(10, Seq((1, 1), (2, 1), (3, 1), (9, 1)))
```



**LabeledPoint**：

```
val vd: Vector = Vectors.dense(2, 0, 6)
val pos = LabeledPoint(1, vd)

val vs: Vector = Vectors.sparse(4, Array(0, 1, 2, 3), Array(9, 5, 2, 7))
val neg = LabeledPoint(2, vs)
```



**LibSVM数据读取**：LibSVM是一种特定规则的数据文件

```
数据格式：
Label index:value1 index2: value2 ... 

val conf = new SparkConf().setMaster("local[*]").setAppName("iris")
val sc = new SparkContext(conf)
val mu: RDD[LabeledPoint] = MLUtils.loadLibSVMFile(sc, "dataset/iris.txt")
```



**本地矩阵**：矩阵以列优先存储

```
// 稠密性矩阵
val dense = Matrices.dense(3, 2, Array(1.0, 3.0, 5, 2, 4, 6))

// 稀疏性举证
val sparse = Matrices.sparse(3, 2, Array(0, 1, 3), Array(0, 2, 1), Array(9, 6, 8))
第三个参数：第一个元素代表从第一列开始，第二个元素代表第一列的非零数值的个数，第二个代表第二列与前面所有列的非零数值的个数；
第四个参数是非零数值的行索引；
```



**分布式矩阵**：

```
行矩阵：RowMatrix

行索引矩阵：IndexedRowMatrix

三元组矩阵：MatrixEntry构成，适合稀疏矩阵

分块矩阵：BlockMatrix


```















