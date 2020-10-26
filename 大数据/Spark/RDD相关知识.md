#### Dataset

Dataset是一个强类型，并且类型安全的数据容器，并且提供了结构化查询API和类似RDD一样的命令式API

```
    val spark = new SparkSession.Builder().master("local[*]").appName("stage").getOrCreate()
    import spark.implicits._ // 导入隐式转换
    val persons = spark.createDataset(Seq(Person("a", 10), Person("b", 20), Person("c", 30)))
    // Dataset 支持强类型API
    persons.filter(person => person.age > 10).show()
    // Dataset 支持弱类型API
    persons.filter('age > 10).show()
    persons.filter($"age" > 10).show()
    // Dataset 可以直接编写SQL表达式
    persons.filter("age > 10").show()
  }
```



#### DataFrame

```
val spark = new SparkSession.Builder().master("local[*]").appName("stage").getOrCreate()
import spark.implicits._

val persons = Seq(Person("a", 10), Person("b", 20), Person("c", 30)).toDF()
persons.where('age > 10).select('name).show()
```

创建DataFrame的方式：toDF、createDataFrame、DataFrameReader



注：源码中 -- **type DataFrame = Dataset[Row]**



#### Parquet



Spark默认的读写文件格式是Parquet；

