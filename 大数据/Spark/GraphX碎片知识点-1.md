* 从社交网络、任务关系挖掘、节点之间依赖计算等方面来理解图计算；

* 图的三要素：节点(Vertex)、边(Edge)、边上的关系；
* GraphX整合了data-parallel和graph-parallel，实现数据和图计算的并行化处理和分析；
* GraphX图算法：

```
PageRank：根据搜索引擎计算网页排名；
最短路径：根据图算法找出最短路径；
社群关系挖掘：根据计算图完成社交网络挖掘；
推荐算法：根据图计算给出推荐结果；
```



Spark的每个模块，都有自己的抽象数据结构，GraphX的抽象是弹性分布式属性图，即点和边都带有属性的有向多重图。



* 顶点：RDD[(VertexId, VD)]，**以VertextId为类型的顶点ID**和**以VD为类型的属性**作为参数类型；
* 边：RDD[Edge[ED]]，以Edge[ED]作为参数类型；
* 三元组：RDD[EdgeTriplet[VD, ED]]，其实是对顶点和边作了连接操作；
* 图：Graph[VD, ED]；



<img src="D:\Github\StudyNote\大数据\asset\spark-graphx.png" style="zoom:67%;" />





```
// 创建顶点集合
val users: RDD[(VertexId, (String, String))] = sc.parallelize(Seq((3L, ("rxin", "student")), (7L, ("jgonzal", "postdoc")),(5L, ("franklin", "prof")), (2L, ("istoica", "prof"))))

// 创建边集合
val relationships: RDD[Edge[String]] = sc.parallelize(Seq(Edge(3L, 7L, "collab"), Edge(5L, 3L, "advisor"),
Edge(2L, 5L, "colleague"), Edge(5L, 7L, "pi")))

// 创建默认顶点
val defaultUser = ("John Doe", "Missing")

// 创建图
val graph = Graph(users, relationships, defaultUser)
```



#### 创建顶点

```
val users: RDD[(VertexId, (String, String))] = sc.parallelize(Seq((3L, ("rxin", "student")), (7L, ("jgonzal", "postdoc")),(5L, ("franklin", "prof")), (2L, ("istoica", "prof"))))

或者

val users_other = VertextRDD[(String, String)](users)


```



#### 创建边

```
val relationships: RDD[Edge[String]] = sc.parallelize(Seq(Edge(3L, 7L, "collab"), Edge(5L, 3L, "advisor"),
Edge(2L, 5L, "colleague"), Edge(5L, 7L, "pi")))

或者

import org.apache.spark.graphx.EdgeRDD
val relationships_other: EdgeRDDImpl[String, Nothing] = EdgeRDD.fromEdges(relationships)
```



#### 创建图

```
val users: RDD[(VertexId, (String, String))] = sc.parallelize(Seq((3L, ("rxin", "student")), (7L, ("jgonzal", "postdoc")),(5L, ("franklin", "prof")), (2L, ("istoica", "prof"))))

val relationships: RDD[Edge[String]] = sc.parallelize(Seq(Edge(3L, 7L, "collab"), Edge(5L, 3L, "advisor"),
Edge(2L, 5L, "colleague"), Edge(5L, 7L, "pi")))

# 方法一
val defaultUser = ("John Doe", "Missing")
val graph = Graph(users, relationships, defaultUser)

# 方法二
val relations: RDD[(VertextId, VertextId)] = sc.parallelize(Seq((3L, 7L), (5L, 3L),
(2L, 5L), Edge(5L, 7L)))
val graph: Graph[(String, String), Int] = Graph.fromEdgeTuples(relations, defaultUser)

# 方法三
val graph = Graph.fromEdges(relationships)
```









